# Architecture

## Stack

| Layer | Technology | Purpose |
|---|---|---|
| Mobile App | Flutter + Dart | Participant learning platform |
| Web Dashboard | Next.js + TypeScript | Instructor and admin management interface |
| Backend API | NestJS + TypeScript | Business logic, API, authorization |
| Database | PostgreSQL | Primary persistent data store |
| ORM | Prisma | Database access and migrations |
| Authentication | JWT + Refresh Token | Stateless authentication across all clients |
| Object Storage | S3-compatible | Course materials, certificates, user uploads |
| Cache / Queue | Redis (when required) | Caching, background job queues |
| Push Notifications | Firebase Cloud Messaging (Phase 2) | Mobile push notifications |

---

## System Architecture

```
┌──────────────┐     ┌──────────────────┐
│  Mobile App  │     │  Web Dashboard   │
│  (Flutter)   │     │  (Next.js)       │
│              │     │                  │
│  Peserta     │     │  Instruktur      │
│              │     │  Admin Platform  │
│              │     │  Admin Lembaga   │
└──────┬───────┘     └────────┬─────────┘
       │                      │
       │    REST API (JSON)   │
       └──────────┬───────────┘
                  │
        ┌─────────▼──────────┐
        │   Backend API      │
        │   (NestJS)         │
        │                    │
        │  Business Logic    │
        │  Authorization     │
        │  Validation        │
        └──┬─────┬───────┬───┘
           │     │       │
     ┌─────▼──┐ ┌▼─────┐ ┌▼────────────┐
     │ Postgres│ │Redis │ │S3 Storage   │
     │ (Prisma)│ │      │ │             │
     └────────┘ └──────┘ └─────────────┘
```

### Ownership Rules

- **Backend API** owns all business logic, authorization, validation, and database access
- **Mobile App** and **Web Dashboard** are presentation layers only — they consume the API
- Client applications must not independently implement business rules
- Client applications must not directly access the database
- The API contract is the boundary between clients and backend

---

## Repository Structure

This is a monorepo. All three applications live in the same repository.

```
/
├── context/                              → Project context (this folder)
│   ├── project-overview.md               → Product truth — what the system does
│   ├── architecture.md                   → Technical truth — how it's built
│   ├── api-contracts.md                  → API contract definitions
│   ├── decisions.md                      → Architecture Decision Records
│   ├── build-plan.md                     → Feature implementation plans
│   ├── code-standards.md                 → Implementation rules for all apps
│   ├── library-docs.md                   → Library usage patterns
│   ├── progress-tracker.md               → Project state and history
│   ├── ui-registry.md                    → Built component catalog
│   ├── ui-rules.md                       → Visual design rules
│   ├── ui-tokens.md                      → Design token values
│   └── workflow.md                       → How to use this context
├── apps/
│   ├── api/                              → NestJS Backend API
│   │   ├── src/
│   │   │   ├── modules/                  → Feature modules
│   │   │   │   ├── auth/                 → Authentication module
│   │   │   │   ├── users/                → User management module
│   │   │   │   ├── courses/              → Course management module
│   │   │   │   ├── assessments/          → Assessment module
│   │   │   │   ├── certifications/       → Certification module
│   │   │   │   └── ...
│   │   │   ├── common/                   → Shared utilities, guards, filters, pipes
│   │   │   ├── config/                   → Configuration module
│   │   │   ├── prisma/                   → Prisma service module
│   │   │   ├── app.module.ts             → Root module
│   │   │   └── main.ts                   → Application entry point
│   │   ├── prisma/
│   │   │   ├── schema.prisma             → Database schema
│   │   │   └── migrations/               → Database migrations
│   │   ├── test/
│   │   └── package.json
│   ├── web/                              → Next.js Web Dashboard
│   │   ├── src/
│   │   │   ├── app/                      → App Router pages
│   │   │   ├── components/               → UI components
│   │   │   ├── lib/                      → Utilities, API client, hooks
│   │   │   └── types/                    → TypeScript type definitions
│   │   └── package.json
│   └── mobile/                           → Flutter Mobile App
│       ├── lib/
│       │   ├── core/                     → Core utilities, constants, theme
│       │   ├── features/                 → Feature-based modules
│       │   ├── shared/                   → Shared widgets and services
│       │   └── main.dart                 → Application entry point
│       └── pubspec.yaml
├── packages/                             → Shared packages (if needed)
│   └── shared-types/                     → Shared TypeScript types (API contracts)
├── brief.md                              → Original project brief
└── package.json                          → Root monorepo config
```

---

## Backend Architecture (NestJS)

### Modular Monolith

The backend follows a modular monolith approach. Each feature is a self-contained NestJS module with its own controllers, services, DTOs, and entities.

```
src/modules/courses/
├── courses.module.ts          → Module declaration
├── courses.controller.ts      → HTTP endpoints
├── courses.service.ts         → Business logic
├── dto/
│   ├── create-course.dto.ts   → Request validation
│   ├── update-course.dto.ts
│   └── course-response.dto.ts → Response shape
└── entities/
    └── course.entity.ts       → Domain entity (if needed beyond Prisma)
```

### Module Boundaries

- Modules communicate through their exported services — never through direct database queries across modules
- Each module owns its database tables and is the only module that directly queries them
- If Module A needs data from Module B, it imports Module B and uses its service
- No circular dependencies between modules

### Request Lifecycle

```
HTTP Request
    ↓
Guard (AuthGuard, RolesGuard)
    ↓
Pipe (ValidationPipe — DTO validation)
    ↓
Controller (route handling, parameter extraction)
    ↓
Service (business logic, database access via Prisma)
    ↓
Response DTO (shape the response)
    ↓
HTTP Response
```

---

## Database Schema (Prisma)

### Core Models

```prisma
model User {
  id            String    @id @default(uuid())
  email         String    @unique
  password      String
  fullName      String    @map("full_name")
  phone         String?
  role          Role      @default(PESERTA)
  isActive      Boolean   @default(true) @map("is_active")
  createdAt     DateTime  @default(now()) @map("created_at")
  updatedAt     DateTime  @updatedAt @map("updated_at")

  enrollments   Enrollment[]
  certificates  Certificate[]
  courses       Course[]       @relation("InstructorCourses")

  @@map("users")
}

enum Role {
  PESERTA
  INSTRUKTUR
  ADMIN_PLATFORM
  ADMIN_LEMBAGA
}

model Course {
  id            String    @id @default(uuid())
  title         String
  description   String?
  thumbnail     String?
  status        CourseStatus @default(DRAFT)
  instructorId  String    @map("instructor_id")
  createdAt     DateTime  @default(now()) @map("created_at")
  updatedAt     DateTime  @updatedAt @map("updated_at")

  instructor    User      @relation("InstructorCourses", fields: [instructorId], references: [id])
  modules       Module[]
  enrollments   Enrollment[]
  assessments   Assessment[]
  certificates  Certificate[]

  @@map("courses")
}

enum CourseStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}

model Module {
  id            String    @id @default(uuid())
  courseId       String    @map("course_id")
  title         String
  description   String?
  order         Int
  createdAt     DateTime  @default(now()) @map("created_at")
  updatedAt     DateTime  @updatedAt @map("updated_at")

  course        Course    @relation(fields: [courseId], references: [id])
  materials     Material[]
  assessments   Assessment[]

  @@map("modules")
}

model Material {
  id            String    @id @default(uuid())
  moduleId      String    @map("module_id")
  title         String
  type          MaterialType
  content       String?
  fileUrl       String?   @map("file_url")
  order         Int
  duration      Int?      // in seconds, for video materials
  createdAt     DateTime  @default(now()) @map("created_at")
  updatedAt     DateTime  @updatedAt @map("updated_at")

  module        Module    @relation(fields: [moduleId], references: [id])
  progress      Progress[]

  @@map("materials")
}

enum MaterialType {
  TEXT
  VIDEO
  DOCUMENT
}

model Enrollment {
  id            String    @id @default(uuid())
  userId        String    @map("user_id")
  courseId       String    @map("course_id")
  enrolledAt    DateTime  @default(now()) @map("enrolled_at")
  completedAt   DateTime? @map("completed_at")
  status        EnrollmentStatus @default(ACTIVE)

  user          User      @relation(fields: [userId], references: [id])
  course        Course    @relation(fields: [courseId], references: [id])
  progress      Progress[]

  @@unique([userId, courseId])
  @@map("enrollments")
}

enum EnrollmentStatus {
  ACTIVE
  COMPLETED
  DROPPED
}

model Progress {
  id            String    @id @default(uuid())
  enrollmentId  String    @map("enrollment_id")
  materialId    String    @map("material_id")
  completed     Boolean   @default(false)
  completedAt   DateTime? @map("completed_at")

  enrollment    Enrollment @relation(fields: [enrollmentId], references: [id])
  material      Material   @relation(fields: [materialId], references: [id])

  @@unique([enrollmentId, materialId])
  @@map("progress")
}

model Assessment {
  id            String    @id @default(uuid())
  courseId       String    @map("course_id")
  moduleId      String?   @map("module_id")
  title         String
  type          AssessmentType
  passingScore  Int       @default(70) @map("passing_score")
  timeLimitMin  Int?      @map("time_limit_min")
  createdAt     DateTime  @default(now()) @map("created_at")
  updatedAt     DateTime  @updatedAt @map("updated_at")

  course        Course    @relation(fields: [courseId], references: [id])
  module        Module?   @relation(fields: [moduleId], references: [id])
  questions     Question[]
  attempts      Attempt[]

  @@map("assessments")
}

enum AssessmentType {
  PRE_TEST
  POST_TEST
  QUIZ
}

model Question {
  id            String    @id @default(uuid())
  assessmentId  String    @map("assessment_id")
  text          String
  type          QuestionType
  options       Json?     // array of option objects for multiple choice
  correctAnswer String    @map("correct_answer")
  points        Int       @default(1)
  order         Int

  assessment    Assessment @relation(fields: [assessmentId], references: [id])

  @@map("questions")
}

enum QuestionType {
  MULTIPLE_CHOICE
  TRUE_FALSE
  SHORT_ANSWER
}

model Attempt {
  id            String    @id @default(uuid())
  userId        String    @map("user_id")
  assessmentId  String    @map("assessment_id")
  answers       Json      // array of { questionId, answer }
  score         Int?
  passed        Boolean?
  startedAt     DateTime  @default(now()) @map("started_at")
  completedAt   DateTime? @map("completed_at")

  assessment    Assessment @relation(fields: [assessmentId], references: [id])

  @@map("attempts")
}

model Certificate {
  id            String    @id @default(uuid())
  userId        String    @map("user_id")
  courseId       String    @map("course_id")
  certificateNo String    @unique @map("certificate_no")
  issuedAt      DateTime  @default(now()) @map("issued_at")
  issuedBy      String    @map("issued_by") // Admin Lembaga user ID
  fileUrl       String?   @map("file_url")
  status        CertificateStatus @default(ISSUED)

  user          User      @relation(fields: [userId], references: [id])
  course        Course    @relation(fields: [courseId], references: [id])

  @@unique([userId, courseId])
  @@map("certificates")
}

enum CertificateStatus {
  PENDING
  ISSUED
  REVOKED
}
```

### Database Conventions

- Table names: snake_case plural (`users`, `courses`, `enrollments`)
- Column names: snake_case in database, camelCase in Prisma model (`@map` for conversion)
- Primary keys: UUID (`@default(uuid())`)
- Timestamps: `created_at` and `updated_at` on every table
- Soft deletes: use `isActive` boolean or status enum — never hard delete user data
- Indexes: add explicit indexes for frequently queried foreign keys
- Enums: UPPER_SNAKE_CASE values

### Migration Rules

- Every database change requires a Prisma migration
- Migrations are created with `npx prisma migrate dev --name descriptive_name`
- Migration names describe the change: `add_enrollment_table`, `add_passing_score_to_assessment`
- Never modify existing migrations — always create new ones
- Every migration must be traceable to a feature requirement

---

## Authentication

### Strategy

- JWT access token + refresh token
- Access token: short-lived (15 minutes), sent in Authorization header
- Refresh token: long-lived (7 days), stored securely, used to obtain new access tokens
- Passwords hashed with bcrypt
- Role-based access control via custom `@Roles()` decorator and `RolesGuard`

### Token Payload

```typescript
{
  sub: string;     // user ID
  email: string;
  role: Role;
  iat: number;
  exp: number;
}
```

### Auth Flow

```
Registration:
  Client → POST /api/auth/register → Backend creates user → Returns tokens

Login:
  Client → POST /api/auth/login → Backend validates credentials → Returns tokens

Token Refresh:
  Client → POST /api/auth/refresh → Backend validates refresh token → Returns new tokens

Protected Request:
  Client → Authorization: Bearer <access_token> → AuthGuard validates → Route handler
```

### Authorization

Role-based access is enforced at the API level:

| Role | Access Scope |
|---|---|
| PESERTA | Own profile, enrolled courses, own assessments, own certificates |
| INSTRUKTUR | Own profile, own courses (CRUD), participants in own courses, assessment results |
| ADMIN_PLATFORM | All users, all courses, all activity data, platform reports |
| ADMIN_LEMBAGA | All certification data, certificate issuance, participant verification |

Authorization is always enforced by the backend — never by the client.

---

## API Design

### Conventions

- RESTful JSON API
- Base path: `/api`
- Versioning: `/api/v1/` (when versioning is needed)
- Request bodies: JSON
- Response format: consistent envelope

### Response Envelope

```json
// Success
{
  "success": true,
  "data": { ... },
  "meta": { "page": 1, "limit": 20, "total": 100 }
}

// Error
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human readable error message",
    "details": [ ... ]
  }
}
```

### Pagination

All list endpoints support pagination:

```
GET /api/courses?page=1&limit=20&sort=createdAt&order=desc
```

Response includes `meta` with pagination info.

### Error Codes

| HTTP Status | Code | Meaning |
|---|---|---|
| 400 | VALIDATION_ERROR | Request body validation failed |
| 401 | UNAUTHORIZED | Missing or invalid token |
| 403 | FORBIDDEN | Valid token but insufficient role/permissions |
| 404 | NOT_FOUND | Resource does not exist |
| 409 | CONFLICT | Duplicate or conflicting resource |
| 500 | INTERNAL_ERROR | Unexpected server error |

---

## Object Storage

### Buckets

| Bucket | Path Pattern | Content |
|---|---|---|
| materials | `courses/{courseId}/materials/{materialId}/{filename}` | Course materials (videos, documents) |
| certificates | `certificates/{certificateId}/{filename}` | Generated certificate PDFs |
| avatars | `users/{userId}/avatar.{ext}` | User profile photos |

### Rules

- All file uploads go through the Backend API — clients never write directly to storage
- The API generates pre-signed URLs for large file uploads when needed
- File URLs stored in the database are relative paths — the API constructs full URLs
- Access control: the API verifies user authorization before serving or creating signed URLs

---

## Invariants

Rules the AI agent must never violate:

- Backend API owns all business logic — clients are presentation layers only
- Clients never access the database directly — all data flows through the API
- Every API endpoint validates input with DTOs and class-validator
- Every API endpoint enforces authentication (except /auth/register, /auth/login)
- Every API endpoint enforces role-based authorization
- Database queries always scope to authorized data — never return data the user shouldn't see
- Database changes always go through Prisma migrations — never raw SQL in production
- Module boundaries are respected — no cross-module direct database queries
- File uploads always go through the API — clients never write directly to storage
- Enums are defined once in Prisma schema and reflected in API DTOs — no duplicate enum definitions
- Error responses always use the standard envelope — never raw error objects
- Passwords are never logged, never returned in API responses, never stored in plain text
