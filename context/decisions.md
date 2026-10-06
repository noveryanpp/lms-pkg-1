# Architecture Decisions

Record of architectural decisions made during the project. Each decision is numbered and includes context, the decision itself, and consequences. Decisions are permanent unless explicitly superseded by a later decision.

AI agents must check this file before proposing any architectural change. If an existing decision conflicts with what you want to implement — stop, reference the decision, explain the conflict, and request approval before proceeding.

---

## How to Use

### Reading Decisions

Before implementing anything that touches architecture, infrastructure, or cross-application patterns:

1. Check if a relevant decision already exists
2. Follow the existing decision — do not silently override it
3. If the decision seems wrong for the current situation — flag it, propose an amendment, wait for approval

### Recording New Decisions

When a new architectural decision is made during implementation:

1. Add a new entry with the next ADR number
2. Fill in all fields: Status, Context, Decision, Consequences
3. Link the decision to the feature or requirement that triggered it

### Amending Decisions

If a previous decision needs to change:

1. Do not modify the original entry
2. Add a new entry that references and supersedes the original
3. Mark the original as `Superseded by ADR-XXX`

---

## Decision Format

```
### ADR-XXX: Title

**Status:** Accepted | Superseded by ADR-XXX | Rejected
**Date:** YYYY-MM-DD
**Feature:** Related feature or requirement

**Context:** Why is this decision needed?

**Decision:** What was decided?

**Consequences:** What follows from this decision?
```

---

## Decisions

### ADR-001: Modular Monolith for Backend

**Status:** Accepted
**Date:** 2026-10-06
**Feature:** System Architecture

**Context:** The system has multiple feature domains (auth, courses, assessments, certifications) that could be organized as microservices or as modules within a single application. The team size and project scope do not justify the operational complexity of microservices at this stage.

**Decision:** The NestJS backend uses a modular monolith architecture. Each feature is a self-contained NestJS module with its own controllers, services, and DTOs. Modules communicate through exported services — never through direct cross-module database queries.

**Consequences:**
- Simpler deployment and infrastructure — single application
- Clear module boundaries enforce separation of concerns
- Modules can be extracted to separate services later if needed
- All modules share the same database and Prisma client
- Circular dependencies between modules must be avoided

---

### ADR-002: Three-Application Architecture

**Status:** Accepted
**Date:** 2026-10-06
**Feature:** System Architecture

**Context:** The system serves two distinct user groups with different needs: participants who learn primarily on mobile devices, and instructors/administrators who manage content from desktop browsers. A single application cannot optimally serve both use cases.

**Decision:** The system consists of three independently developed applications — Flutter mobile app (participants), Next.js web dashboard (instructors and administrators), and NestJS backend API (business logic). All business logic resides in the backend. Client applications are presentation layers only.

**Consequences:**
- Mobile app and web dashboard develop independently but share the same API
- API contracts must be defined before client implementation
- Business logic changes only require backend updates — clients consume the API
- More initial setup complexity but better long-term maintainability
- No business logic duplication across client applications

---

### ADR-003: JWT Authentication with Refresh Tokens

**Status:** Accepted
**Date:** 2026-10-06
**Feature:** REQ-AUTH-001

**Context:** The system needs stateless authentication that works across both mobile and web clients. Session-based auth is complex with mobile apps. Third-party auth providers add external dependencies for Phase 1.

**Decision:** Use JWT access tokens (15-minute expiry) with refresh tokens (7-day expiry). Access tokens are sent in the Authorization header. Refresh tokens are used to obtain new access tokens. Passwords are hashed with bcrypt.

**Consequences:**
- Stateless — no server-side session storage needed
- Works consistently across Flutter and Next.js
- Short access token expiry limits the damage window of stolen tokens
- Refresh token rotation must be implemented for security
- Token blacklisting can be added via Redis if needed

---

### ADR-004: PostgreSQL with Prisma ORM

**Status:** Accepted
**Date:** 2026-10-06
**Feature:** System Architecture

**Context:** The system needs a relational database for structured data with strong consistency. An ORM is needed for type safety and migration management.

**Decision:** PostgreSQL as the primary database. Prisma as the ORM — providing type-safe database access, schema management, and migrations.

**Consequences:**
- Prisma schema is the source of truth for database structure
- All database changes go through Prisma migrations
- Type-safe queries throughout the backend
- Prisma Client is auto-generated from schema — always reflects current DB state
- Raw SQL should be avoided unless Prisma cannot express the query

---

### ADR-005: S3-Compatible Object Storage

**Status:** Accepted
**Date:** 2026-10-06
**Feature:** System Architecture

**Context:** The system needs to store files — course materials (videos, documents), user avatars, and generated certificates. These files should not be stored in the database or local filesystem.

**Decision:** Use S3-compatible object storage. File uploads go through the Backend API. The API stores files in organized bucket paths and saves references in the database.

**Consequences:**
- Files are stored outside the application — scalable and cost-effective
- S3-compatible means flexibility to use AWS S3, MinIO, DigitalOcean Spaces, etc.
- Backend controls access — clients never write directly to storage
- Pre-signed URLs can be used for large uploads and direct downloads

---

### ADR-006: Peserta on Mobile Only, Admin/Instruktur on Web Only

**Status:** Accepted
**Date:** 2026-10-06
**Feature:** Platform Strategy

**Context:** The brief recommends mobile as the primary platform for learning. Building the full learning experience on both mobile and web would double the frontend development effort without clear benefit.

**Decision:** Peserta (participants) use the mobile app exclusively for all learning activities. Instruktur, Admin Platform, and Admin Lembaga use the web dashboard exclusively for management. The backend API serves both equally.

**Consequences:**
- Mobile app only needs to implement participant-facing features
- Web dashboard only needs to implement management-facing features
- No feature duplication across clients
- Clear scope for each frontend team
- If web access for participants is needed later, it can be added as a separate decision

---

### ADR-007: Monorepo Structure

**Status:** Accepted
**Date:** 2026-10-06
**Feature:** System Architecture

**Context:** Three applications that share API contracts and potentially shared types need a development structure that keeps them synchronized.

**Decision:** All three applications live in a single monorepo under `/apps/api`, `/apps/web`, and `/apps/mobile`. Shared packages live under `/packages/`.

**Consequences:**
- Shared types and contracts are easy to keep in sync
- Single repository for code reviews and version control
- Each application maintains its own package.json and dependencies
- CI/CD can build and deploy applications independently
- Developers can work across applications without switching repositories
