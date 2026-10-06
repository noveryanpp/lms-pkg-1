# Build Plan

## Core Principle

Features are built as vertical slices — from requirement through API contract, backend implementation, and client implementation. Each feature is fully specified before implementation begins. No implementation without a defined API contract. No client work without a working backend endpoint.

---

## How to Use

### Starting a Feature

1. Read the feature section in this file
2. Check requirements (REQ-xxx identifiers)
3. Check business rules
4. Verify the API contract exists in `api-contracts.md`
5. If any of the above is missing — stop and flag the gap

### Implementation Order per Feature

```
Requirements defined
    ↓
Business rules documented
    ↓
Data model designed (Prisma schema)
    ↓
API contract defined (api-contracts.md)
    ↓
Backend implementation (NestJS)
    ↓
Mobile implementation (Flutter) — if feature affects Peserta
    ↓
Dashboard implementation (Next.js) — if feature affects Instruktur/Admin
    ↓
Tests
    ↓
Progress tracker updated
```

### Definition of Done

A feature is complete when all applicable items are satisfied:

```
[ ] Requirements implemented (all REQ-xxx for this feature)
[ ] Business rules implemented and enforced by backend
[ ] Database migration created (if data model changed)
[ ] API contract documented in api-contracts.md
[ ] Backend implementation completed
[ ] Authorization enforced for all endpoints
[ ] Input validation implemented (DTOs)
[ ] Mobile implementation completed (if applicable)
[ ] Dashboard implementation completed (if applicable)
[ ] Error handling implemented (backend + clients)
[ ] Tests added (at minimum: API endpoint tests)
[ ] Existing tests still pass
[ ] Progress tracker updated
[ ] No unrelated changes introduced
```

Skip items that don't apply — a backend-only change doesn't need mobile tasks. A data migration doesn't need UI work.

---

## Feature 1: Akses Pengguna (User Access)

### Requirements

- **REQ-AUTH-001:** Users can register a new account with email and password
- **REQ-AUTH-002:** Users can login with email and password
- **REQ-AUTH-003:** Authentication uses JWT access token + refresh token
- **REQ-AUTH-004:** Access tokens expire after 15 minutes, refresh tokens after 7 days
- **REQ-AUTH-005:** New registrations always have role PESERTA
- **REQ-AUTH-006:** Instruktur and Admin accounts are created by Admin Platform
- **REQ-PROFILE-001:** Users can view and update their own profile
- **REQ-PROFILE-002:** Profile includes: full name, email, phone, avatar
- **REQ-RBAC-001:** Role-based access control with 4 roles: PESERTA, INSTRUKTUR, ADMIN_PLATFORM, ADMIN_LEMBAGA
- **REQ-RBAC-002:** Each API endpoint enforces role authorization
- **REQ-RBAC-003:** Users can only access data they are authorized for

### Business Rules

- Email must be unique across all users
- Password minimum 8 characters, hashed with bcrypt before storage
- Tokens are stateless — no server-side session storage
- Refresh token rotation: issuing a new access token invalidates the old refresh token
- Deactivated users (`isActive: false`) cannot login
- Admin Platform can create users with any role
- Users cannot change their own role

### Dependencies

- None — this is the foundation feature

### Data Changes

- `User` model with all fields as defined in architecture.md
- `Role` enum: PESERTA, INSTRUKTUR, ADMIN_PLATFORM, ADMIN_LEMBAGA
- Initial Prisma migration

### API Changes

- See `api-contracts.md` → Auth Endpoints (register, login, refresh)
- User profile endpoints (GET, PATCH) — to be defined in api-contracts.md
- Admin user management endpoints — to be defined in api-contracts.md

### Backend Tasks

- [ ] Auth module: register, login, refresh endpoints
- [ ] JWT strategy and AuthGuard
- [ ] RolesGuard with @Roles() decorator
- [ ] Password hashing with bcrypt
- [ ] User module: profile CRUD
- [ ] Admin user creation endpoint (with role assignment)
- [ ] Validation DTOs for all endpoints
- [ ] Error handling with standard envelope

### Mobile Tasks (Flutter)

- [ ] Login screen
- [ ] Registration screen
- [ ] Secure token storage
- [ ] API client with token refresh interceptor
- [ ] Profile view screen
- [ ] Profile edit screen

### Dashboard Tasks (Next.js)

- [ ] Login page
- [ ] Auth state management with token storage
- [ ] API client with token refresh interceptor
- [ ] Protected route middleware
- [ ] User management page (Admin)
- [ ] Create user form with role selection (Admin)

### Tests

- [ ] Auth endpoints: register, login, refresh — happy path and error cases
- [ ] Role-based access: verify each role can only access permitted endpoints
- [ ] Token expiry and refresh flow

---

## Feature 2: Manajemen Kursus (Course Management)

### Requirements

- **REQ-COURSE-001:** Instruktur can create a new course
- **REQ-COURSE-002:** Instruktur can edit course details (title, description, thumbnail)
- **REQ-COURSE-003:** Instruktur can manage modules within a course (create, edit, reorder, delete)
- **REQ-COURSE-004:** Instruktur can manage materials within modules (text, video, documents)
- **REQ-COURSE-005:** Instruktur can publish a course (DRAFT → PUBLISHED)
- **REQ-COURSE-006:** Instruktur can archive a course (PUBLISHED → ARCHIVED)
- **REQ-COURSE-007:** Peserta can browse and view published courses
- **REQ-COURSE-008:** Peserta can enroll in a published course
- **REQ-COURSE-009:** Peserta can view course materials after enrollment
- **REQ-COURSE-010:** Peserta progress is tracked per material
- **REQ-COURSE-011:** Admin Platform can view and manage all courses
- **REQ-COURSE-012:** Course materials support text, video, and document types

### Business Rules

- Only Instruktur who created the course can edit it (unless Admin Platform)
- Courses must have at least one module with at least one material before publishing
- Published courses cannot be deleted — only archived
- Archived courses remain visible to enrolled participants but not to new enrollments
- Enrollment creates a record linking user to course — cannot enroll twice
- Material progress is tracked per enrollment per material
- Course completion is determined by completing all materials (and passing required assessments — see Feature 3)
- File uploads (video, documents) go through the API to S3 storage

### Dependencies

- Feature 1: Akses Pengguna (authentication and roles)

### Data Changes

- `Course` model
- `Module` model
- `Material` model
- `Enrollment` model
- `Progress` model
- `CourseStatus` enum: DRAFT, PUBLISHED, ARCHIVED
- `MaterialType` enum: TEXT, VIDEO, DOCUMENT
- `EnrollmentStatus` enum: ACTIVE, COMPLETED, DROPPED

### API Changes

- Course CRUD endpoints — to be defined in api-contracts.md
- Module CRUD endpoints — to be defined
- Material CRUD endpoints — to be defined
- Enrollment endpoints — to be defined
- Progress tracking endpoints — to be defined

### Backend Tasks

- [ ] Course module: CRUD endpoints with authorization
- [ ] Module management within courses
- [ ] Material management with file upload
- [ ] Course publishing workflow (draft → published → archived)
- [ ] Enrollment service
- [ ] Progress tracking service
- [ ] File upload to S3 storage
- [ ] Validation DTOs

### Mobile Tasks (Flutter)

- [ ] Course browse / listing screen
- [ ] Course detail screen
- [ ] Enrollment action
- [ ] Course learning screen (materials viewer)
- [ ] Video player for video materials
- [ ] Document viewer
- [ ] Progress tracking UI

### Dashboard Tasks (Next.js)

- [ ] Course listing page (Instruktur: own courses, Admin: all courses)
- [ ] Course create / edit form
- [ ] Module management UI (add, edit, reorder, delete)
- [ ] Material management UI (add, edit, reorder, file upload)
- [ ] Course publishing controls
- [ ] Enrolled participants view

### Tests

- [ ] Course CRUD with authorization
- [ ] Publishing workflow validation
- [ ] Enrollment constraints (no duplicate enrollment)
- [ ] Progress tracking accuracy

---

## Feature 3: Asesmen & Evaluasi (Assessment & Evaluation)

### Requirements

- **REQ-ASSESS-001:** Instruktur can create assessments linked to a course or module
- **REQ-ASSESS-002:** Assessment types: PRE_TEST, POST_TEST, QUIZ
- **REQ-ASSESS-003:** Instruktur can add questions to assessments (multiple choice, true/false, short answer)
- **REQ-ASSESS-004:** Instruktur can set a passing score per assessment
- **REQ-ASSESS-005:** Instruktur can set an optional time limit per assessment
- **REQ-ASSESS-006:** Peserta can take assessments after enrollment
- **REQ-ASSESS-007:** System automatically scores assessments on submission
- **REQ-ASSESS-008:** Peserta can view their assessment results and history
- **REQ-ASSESS-009:** Instruktur can view assessment results for their courses
- **REQ-ASSESS-010:** Admin Platform can view all assessment data

### Business Rules

- PRE_TEST is taken before starting course content
- POST_TEST is taken after completing course content
- QUIZ is taken per module during the course
- Automatic scoring: compare submitted answers against correct answers, calculate percentage
- Passing is determined by score >= assessment's passingScore
- Peserta can retake failed assessments (unless restricted — future enhancement)
- Assessment attempt records are immutable once submitted
- Time limit (if set) is enforced by the backend — expired attempts are auto-submitted

### Dependencies

- Feature 1: Akses Pengguna (authentication)
- Feature 2: Manajemen Kursus (courses, modules, enrollment)

### Data Changes

- `Assessment` model
- `Question` model
- `Attempt` model
- `AssessmentType` enum: PRE_TEST, POST_TEST, QUIZ
- `QuestionType` enum: MULTIPLE_CHOICE, TRUE_FALSE, SHORT_ANSWER

### API Changes

- Assessment CRUD endpoints — to be defined in api-contracts.md
- Question CRUD endpoints — to be defined
- Attempt submission and results endpoints — to be defined

### Backend Tasks

- [ ] Assessment module: CRUD with course/module linkage
- [ ] Question management within assessments
- [ ] Attempt submission with automatic scoring
- [ ] Time limit enforcement
- [ ] Results and history queries
- [ ] Validation DTOs

### Mobile Tasks (Flutter)

- [ ] Assessment taking screen
- [ ] Question display (per question type)
- [ ] Timer UI (for timed assessments)
- [ ] Assessment results screen
- [ ] Assessment history screen

### Dashboard Tasks (Next.js)

- [ ] Assessment creation form
- [ ] Question builder UI
- [ ] Assessment configuration (passing score, time limit)
- [ ] Results overview per course/assessment
- [ ] Individual participant results

### Tests

- [ ] Automatic scoring accuracy (multiple choice, true/false, short answer)
- [ ] Passing/failing threshold
- [ ] Time limit enforcement
- [ ] Authorization (Peserta can only see own results, Instruktur only own courses)

---

## Feature 4: Sertifikasi (Certification)

### Requirements

- **REQ-CERT-001:** Admin Lembaga can view participants eligible for certification
- **REQ-CERT-002:** Eligibility: course completed + post-test passed
- **REQ-CERT-003:** Admin Lembaga can issue a digital certificate
- **REQ-CERT-004:** Certificates have a unique certificate number
- **REQ-CERT-005:** Certificates are generated as PDF and stored in S3
- **REQ-CERT-006:** Peserta can view and download their certificates
- **REQ-CERT-007:** Admin Lembaga can view certification history
- **REQ-CERT-008:** Admin Lembaga can revoke a certificate if needed

### Business Rules

- Certificate eligibility is determined by the system — not manually
- A participant must have completed the course AND passed the post-test to be eligible
- One certificate per participant per course
- Certificate number format: to be defined (e.g., `CERT-{YEAR}-{SEQUENTIAL}`)
- Certificate PDF is generated by the backend and stored in S3
- Revoking a certificate changes status to REVOKED but does not delete the record
- Revoked certificates are not visible to the participant

### Dependencies

- Feature 1: Akses Pengguna (authentication, roles)
- Feature 2: Manajemen Kursus (course completion)
- Feature 3: Asesmen & Evaluasi (post-test passing)

### Data Changes

- `Certificate` model
- `CertificateStatus` enum: PENDING, ISSUED, REVOKED

### API Changes

- Certification eligibility query endpoints — to be defined
- Certificate issuance endpoint — to be defined
- Certificate listing and download endpoints — to be defined

### Backend Tasks

- [ ] Certification module: eligibility check service
- [ ] Certificate issuance with PDF generation
- [ ] Certificate number generation
- [ ] S3 storage for certificate PDFs
- [ ] Certificate revocation
- [ ] Certification history queries
- [ ] Validation DTOs

### Mobile Tasks (Flutter)

- [ ] Certificates listing screen
- [ ] Certificate detail / download screen

### Dashboard Tasks (Next.js)

- [ ] Eligible participants listing (Admin Lembaga)
- [ ] Certificate issuance action
- [ ] Certification history page
- [ ] Certificate revocation action

### Tests

- [ ] Eligibility check (requires completion + passing score)
- [ ] Certificate uniqueness (one per user per course)
- [ ] Revocation flow
- [ ] Authorization (only Admin Lembaga can issue/revoke)

---

## Feature 5: Dashboard

### Requirements

- **REQ-DASH-001:** Peserta sees: enrolled courses, learning progress, assessment results, certificates
- **REQ-DASH-002:** Instruktur sees: own course data, participant count, assessment results summary
- **REQ-DASH-003:** Admin Platform sees: user count, course count, enrollment count, activity summary
- **REQ-DASH-004:** Dashboard data is current — reflects latest state

### Business Rules

- Dashboard is the landing page after login for all roles
- Data is scoped per role — users only see data they are authorized for
- Dashboard does not replace detailed pages — it shows summary and quick access
- Statistics are computed server-side and returned via API

### Dependencies

- Feature 1: Akses Pengguna (authentication, roles)
- Feature 2: Manajemen Kursus (course and enrollment data)
- Feature 3: Asesmen & Evaluasi (assessment results)
- Feature 4: Sertifikasi (certificate data)

### Data Changes

- None — dashboard reads from existing models

### API Changes

- Dashboard summary endpoints per role — to be defined

### Backend Tasks

- [ ] Dashboard module: summary endpoints per role
- [ ] Peserta summary: enrolled courses count, overall progress, recent results, certificates count
- [ ] Instruktur summary: own courses count, total participants, average scores
- [ ] Admin summary: user counts (by role), course counts (by status), enrollment counts, recent activity

### Mobile Tasks (Flutter)

- [ ] Peserta dashboard screen
- [ ] Quick access to enrolled courses, recent results, certificates

### Dashboard Tasks (Next.js)

- [ ] Instruktur dashboard page
- [ ] Admin Platform dashboard page
- [ ] Summary stat cards
- [ ] Recent activity feed

### Tests

- [ ] Dashboard data accuracy per role
- [ ] Authorization scoping

---

## Feature 6: Administrasi (Administration)

### Requirements

- **REQ-ADMIN-001:** Admin Platform can manage all users (list, create, update, deactivate)
- **REQ-ADMIN-002:** Admin Platform can manage all courses (list, view, archive)
- **REQ-ADMIN-003:** Admin Platform can manage all assessments (list, view)
- **REQ-ADMIN-004:** Admin Platform can view platform activity and basic reports
- **REQ-ADMIN-005:** Admin Lembaga can manage certification data (covered in Feature 4)
- **REQ-ADMIN-006:** Admin Platform can view enrollment data across all courses

### Business Rules

- Admin Platform has the highest access level — can view and manage all platform data
- Admin Platform cannot delete users — only deactivate (isActive: false)
- Admin Platform can assign any role when creating a user
- Reports include: user count by role, course count by status, enrollment statistics
- Admin actions should be logged for audit purposes (future enhancement)

### Dependencies

- Feature 1: Akses Pengguna (user management)
- Feature 2: Manajemen Kursus (course management)
- Feature 3: Asesmen & Evaluasi (assessment data)
- Feature 4: Sertifikasi (certification data — Admin Lembaga scope)

### Data Changes

- None — administration operates on existing models

### API Changes

- Admin-specific endpoints for user management, reporting — to be defined

### Backend Tasks

- [ ] Admin user management endpoints (list, create, update role, deactivate)
- [ ] Admin course overview endpoints
- [ ] Admin enrollment statistics
- [ ] Basic reporting endpoints

### Mobile Tasks (Flutter)

- None — administration is web-only

### Dashboard Tasks (Next.js)

- [ ] User management page with list, create, edit, deactivate actions
- [ ] Course overview page (all courses)
- [ ] Enrollment statistics page
- [ ] Basic reports page

### Tests

- [ ] Admin CRUD operations
- [ ] Authorization (only Admin Platform)
- [ ] User deactivation prevents login

---

## Feature Count

| Feature | Backend | Mobile | Dashboard |
|---|---|---|---|
| 1. Akses Pengguna | ✅ | ✅ | ✅ |
| 2. Manajemen Kursus | ✅ | ✅ | ✅ |
| 3. Asesmen & Evaluasi | ✅ | ✅ | ✅ |
| 4. Sertifikasi | ✅ | ✅ | ✅ |
| 5. Dashboard | ✅ | ✅ | ✅ |
| 6. Administrasi | ✅ | — | ✅ |

### Recommended Implementation Order

```
1. Akses Pengguna      → Foundation. Everything depends on this.
2. Manajemen Kursus    → Core product. Depends on auth.
3. Asesmen & Evaluasi  → Depends on courses and enrollment.
4. Sertifikasi         → Depends on course completion and assessment.
5. Dashboard           → Depends on all data being available.
6. Administrasi        → Can run partially in parallel with 2-5.
```
