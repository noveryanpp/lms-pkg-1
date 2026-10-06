# Code Standards

Implementation rules and conventions for the entire project across all three applications. The AI agent must follow these in every session without exception. These rules prevent pattern drift across sessions and across applications.

---

## Engineering Mindset

The AI agent on this project operates as a senior engineer. This means:

- **Think before implementing** — understand what is being built and why before writing a single line
- **Read context files first** — never assume, always verify against architecture.md, project-overview.md, and api-contracts.md
- **Scope is sacred** — only build what the current feature requires. Never go beyond scope even if it seems helpful
- **Every feature must be testable** — if it cannot be verified immediately after implementation, it is incomplete
- **Clean over clever** — simple readable code that a junior developer can understand is always preferred over clever abstractions
- **One thing at a time** — complete one feature fully before touching the next
- **Failures are expected** — wrap operations in try/catch, log failures, never let one failure crash everything
- **Contracts are law** — never invent an API field, endpoint, database column, or business rule that is not documented

---

## AI Stop Conditions

The AI must stop and ask instead of guessing in these situations:

### Requirement Ambiguity

Stop if:
- A business rule is unclear or missing from build-plan.md
- Acceptance criteria are ambiguous
- User behavior is undefined for an edge case
- A REQ-xxx identifier is referenced but not defined

### API Ambiguity

Stop if:
- An endpoint is not defined in api-contracts.md
- Response shape is unclear or contradictory
- Authorization behavior is not specified
- Error handling for a specific case is not documented

### Data Ambiguity

Stop if:
- Relationship between entities is unclear
- A new column or table is needed but not in the Prisma schema
- Data ownership or lifecycle is undefined
- A migration would change existing data

### Architecture Ambiguity

Stop if:
- Implementation requires a pattern not established in architecture.md
- An existing ADR in decisions.md would be contradicted
- A new infrastructure dependency is required (Redis, external API, etc.)
- A module boundary would be violated

### UI Ambiguity

Stop if:
- No design exists for a screen or component
- Visual behavior is undefined
- A new component pattern is needed but not in ui-registry.md
- Platform-specific behavior is unclear (mobile vs web)

### Cross-Application Ambiguity

Stop if:
- Client needs to implement logic that seems like it belongs in the backend
- Backend returns data that no contract describes
- Mobile and web need different behavior for the same feature and it's not documented

**In all cases:** identify the ambiguity, explain why it matters, present reasonable options when useful, request a decision, only then continue.

---

## General TypeScript Rules

Applied to both NestJS backend and Next.js dashboard.

- Strict mode enabled — no exceptions
- Never use `any` — use `unknown` and narrow the type
- Never use type assertions (`as SomeType`) unless absolutely necessary and commented why
- All function parameters and return types must be explicitly typed
- Use `type` for object shapes and unions — use `interface` only when extension is needed
- All async functions must have proper error handling — never let promises float unhandled
- Use `const` by default — only use `let` when reassignment is necessary

---

## NestJS Backend Conventions

### Module Structure

```
src/modules/{feature}/
├── {feature}.module.ts          → Module declaration
├── {feature}.controller.ts      → HTTP endpoints
├── {feature}.service.ts         → Business logic
├── dto/
│   ├── create-{feature}.dto.ts  → Request validation
│   ├── update-{feature}.dto.ts
│   └── {feature}-response.dto.ts
└── guards/                      → Feature-specific guards (if any)
```

### Controller Pattern

```typescript
@Controller('api/courses')
@UseGuards(AuthGuard, RolesGuard)
export class CoursesController {
  constructor(private readonly coursesService: CoursesService) {}

  @Post()
  @Roles(Role.INSTRUKTUR, Role.ADMIN_PLATFORM)
  async create(@Body() dto: CreateCourseDto, @Req() req: AuthRequest) {
    const course = await this.coursesService.create(dto, req.user.id);
    return { success: true, data: course };
  }

  @Get()
  async findAll(@Query() query: PaginationDto) {
    const result = await this.coursesService.findAll(query);
    return { success: true, data: result.data, meta: result.meta };
  }
}
```

### Service Pattern

```typescript
@Injectable()
export class CoursesService {
  constructor(private readonly prisma: PrismaService) {}

  async create(dto: CreateCourseDto, instructorId: string): Promise<Course> {
    return this.prisma.course.create({
      data: {
        ...dto,
        instructorId,
        status: CourseStatus.DRAFT,
      },
    });
  }
}
```

### DTO Pattern

```typescript
import { IsString, IsNotEmpty, IsOptional, MinLength } from 'class-validator';

export class CreateCourseDto {
  @IsString()
  @IsNotEmpty()
  @MinLength(3)
  title: string;

  @IsString()
  @IsOptional()
  description?: string;
}
```

### Rules

- Controllers handle HTTP concerns only — no business logic
- Services contain all business logic and database access
- DTOs validate all incoming data — never trust client input
- Always use class-validator decorators for validation
- Always return the standard response envelope
- Always scope queries to authorized data
- Never expose internal error details in responses
- Log errors with context prefix: `[CoursesService.create]`

---

## Next.js Dashboard Conventions

### App Router Structure

```
src/app/
├── layout.tsx                    → Root layout
├── page.tsx                      → Login/redirect
├── (auth)/
│   └── login/page.tsx            → Login page
├── (dashboard)/
│   ├── layout.tsx                → Dashboard layout with navbar
│   ├── page.tsx                  → Dashboard home
│   ├── users/page.tsx            → User management
│   ├── courses/
│   │   ├── page.tsx              → Course list
│   │   └── [id]/page.tsx         → Course detail/edit
│   └── ...
```

### Component Structure

```typescript
'use client'; // only if needed

// 1. External imports
import { useState } from 'react';

// 2. Internal imports
import { Button } from '@/components/ui/button';
import { apiClient } from '@/lib/api-client';

// 3. Type definitions
type Props = {
  courseId: string;
  title: string;
};

// 4. Component
export function CourseCard({ courseId, title }: Props) {
  // state
  // derived values
  // handlers
  // return JSX
}
```

### API Client Pattern

```typescript
// lib/api-client.ts
// Centralized API client that handles:
// - Base URL configuration
// - JWT token attachment
// - Token refresh on 401
// - Standard error handling

class ApiClient {
  async get<T>(path: string, params?: Record<string, string>): Promise<ApiResponse<T>> { ... }
  async post<T>(path: string, body?: unknown): Promise<ApiResponse<T>> { ... }
  async patch<T>(path: string, body?: unknown): Promise<ApiResponse<T>> { ... }
  async delete<T>(path: string): Promise<ApiResponse<T>> { ... }
}

export const apiClient = new ApiClient();
```

### Rules

- App Router only — no Pages Router
- All components are Server Components by default
- Only add `'use client'` when the component requires state, effects, or browser APIs
- Data fetching happens through the API client — never direct database access
- No business logic in components — components are presentation only
- Token management handled by a centralized API client or auth provider
- Protected routes enforced by middleware or layout-level auth check
- No inline styles — all styling via design tokens

---

## Flutter / Dart Conventions

### Feature Structure

```
lib/features/{feature}/
├── screens/                     → Screen widgets
│   ├── course_list_screen.dart
│   └── course_detail_screen.dart
├── widgets/                     → Feature-specific widgets
│   └── course_card.dart
├── providers/                   → State management (Riverpod / Bloc / etc.)
│   └── course_provider.dart
├── models/                      → Data models for this feature
│   └── course_model.dart
└── services/                    → API service for this feature
    └── course_service.dart
```

### Naming Conventions

- Files: snake_case — `course_list_screen.dart`, `course_model.dart`
- Classes: PascalCase — `CourseListScreen`, `CourseModel`
- Variables and functions: camelCase — `fetchCourses()`, `courseTitle`
- Constants: camelCase or SCREAMING_SNAKE_CASE — `defaultPageSize` or `DEFAULT_PAGE_SIZE`
- Private members: prefix with underscore — `_isLoading`, `_fetchData()`

### API Service Pattern

```dart
class CourseService {
  final ApiClient _apiClient;

  CourseService(this._apiClient);

  Future<ApiResponse<List<Course>>> getCourses({int page = 1, int limit = 20}) async {
    return _apiClient.get('/api/courses', queryParams: {'page': '$page', 'limit': '$limit'});
  }

  Future<ApiResponse<Course>> getCourse(String id) async {
    return _apiClient.get('/api/courses/$id');
  }
}
```

### Rules

- Feature-based folder structure — not layer-based
- Each feature is self-contained with its own screens, widgets, providers, models, services
- Shared widgets live in `lib/shared/widgets/`
- Core utilities, constants, and theme live in `lib/core/`
- API client with JWT token management lives in `lib/core/`
- Models match API response contracts exactly — no invented fields
- No business logic in widgets — widgets are presentation only
- State management approach: to be decided (TBD — will be recorded in decisions.md)

---

## File and Folder Naming

### Backend (NestJS)

- Folders: kebab-case — `user-management`, `course-materials`
- Files: kebab-case with type suffix — `courses.controller.ts`, `create-course.dto.ts`
- One class per file
- Module, controller, service follow `{feature}.{type}.ts` pattern

### Dashboard (Next.js)

- Folders: kebab-case — `course-editor`, `user-management`
- Component files: PascalCase — `CourseCard.tsx`, `UserTable.tsx`
- Utility files: camelCase — `apiClient.ts`, `formatDate.ts`
- One component per file — never export multiple components from one file

### Mobile (Flutter)

- Folders: snake_case — `course_list`, `user_profile`
- Files: snake_case — `course_list_screen.dart`, `course_card.dart`
- One public class per file

---

## API Conventions

### Request/Response

- All requests and responses use JSON
- All dates are ISO 8601 strings
- All IDs are UUIDs
- All list endpoints support pagination: `?page=1&limit=20`
- All responses use the standard envelope (see architecture.md)

### Naming

- URL paths: kebab-case — `/api/courses`, `/api/course-materials`
- URL path params: camelCase — `/api/courses/:courseId`
- Query params: camelCase — `?pageSize=20&sortBy=createdAt`
- Request body fields: camelCase — `{ "fullName": "..." }`
- Response body fields: camelCase — `{ "createdAt": "..." }`

### Validation

- All input validated with class-validator (NestJS) before reaching business logic
- Validation errors return 400 with `VALIDATION_ERROR` code and details array
- Never trust client input — always validate and sanitize on the server

---

## Error Handling

### Backend

- Controllers never throw raw exceptions — use NestJS exception filters
- Services throw typed exceptions: `NotFoundException`, `ForbiddenException`, `ConflictException`
- Global exception filter catches unhandled exceptions and returns standard envelope
- All errors logged with context: `[CoursesService.create] Failed to create course: ${error.message}`
- Never expose stack traces or internal details in API responses

### Dashboard

- API client catches errors and returns typed error objects
- Components show user-friendly error messages — never raw API error text
- Loading and error states for all async operations
- Toast/notification for transient errors, inline for form errors

### Mobile

- API service catches errors and returns typed result objects
- Screens show user-friendly error messages
- Network errors handled gracefully — show retry option
- Loading and error states for all async operations

---

## Database Conventions

- Prisma schema is the source of truth for database structure
- All schema changes go through `npx prisma migrate dev --name descriptive_name`
- Never modify existing migrations — always create new ones
- Table names: snake_case plural (`users`, `courses`)
- Column names: snake_case in DB, camelCase in Prisma (`@map` for conversion)
- Primary keys: UUID
- Timestamps: `created_at` and `updated_at` on every table
- Soft deletes via status field or `isActive` — never hard delete user data
- Every migration must be traceable to a feature requirement in build-plan.md

---

## Comments

- No comments explaining what the code does — code must be self-explanatory
- Comments only for why — explaining a non-obvious decision
- Never leave TODO comments in committed code — track in progress-tracker.md instead

---

## Dependencies

Never install a new package without a clear reason. Before installing anything check:

1. Does the framework already provide this functionality?
2. Is there a simpler native solution?
3. Is this dependency actively maintained?

### Approved Dependencies

**Backend (NestJS):**
- `@nestjs/core`, `@nestjs/common`, `@nestjs/platform-express` — NestJS framework
- `@nestjs/jwt` — JWT handling
- `@nestjs/passport`, `passport`, `passport-jwt` — Authentication
- `@prisma/client` — Database access
- `prisma` — CLI and migrations (dev dependency)
- `class-validator`, `class-transformer` — DTO validation
- `bcrypt` — Password hashing
- `@aws-sdk/client-s3` — S3 storage (or equivalent)
- `zod` — Schema validation where class-validator is not suitable

**Dashboard (Next.js):**
- `next`, `react`, `react-dom` — Framework
- `typescript` — Language
- Styling library — TBD (to be decided and recorded in decisions.md)
- UI component library — TBD
- `zod` — Form validation

**Mobile (Flutter):**
- Core Flutter SDK
- State management package — TBD (to be decided and recorded in decisions.md)
- HTTP client — `dio` or `http`
- Secure storage — `flutter_secure_storage`
- Additional packages — TBD per feature

Do not install any other packages without updating this list first and documenting the reason.

---

## Environment Variables

All environment variables defined in `.env` or `.env.local` for development. Never hardcode any key, URL, or secret anywhere in the codebase.

### Backend

| Variable | Purpose |
|---|---|
| `DATABASE_URL` | PostgreSQL connection string |
| `JWT_SECRET` | JWT signing secret |
| `JWT_EXPIRATION` | Access token expiry (e.g., `15m`) |
| `JWT_REFRESH_EXPIRATION` | Refresh token expiry (e.g., `7d`) |
| `S3_ENDPOINT` | S3 storage endpoint |
| `S3_ACCESS_KEY` | S3 access key |
| `S3_SECRET_KEY` | S3 secret key |
| `S3_BUCKET` | S3 bucket name |
| `REDIS_URL` | Redis connection string (when used) |

### Dashboard

| Variable | Purpose |
|---|---|
| `NEXT_PUBLIC_API_URL` | Backend API base URL |

### Mobile

| Variable / Config | Purpose |
|---|---|
| API base URL | Backend API base URL (in app config) |

Never expose secret keys to client applications. Only the backend has access to secrets.
