# Workflow

How to use this context folder for AI-assisted development of LMS-PKG. This document is for the human developer working with AI agents on this project.

---

## Philosophy

This workflow is built on one principle:

> AI should execute clearly defined requirements and contracts rather than infer missing product or architectural decisions.

Every piece of context exists so the AI can answer specific questions without guessing:

| Question | Source |
|---|---|
| What are we building? | `project-overview.md` |
| What rules must it follow? | `build-plan.md` → business rules per feature |
| How should it be built? | `architecture.md` + `code-standards.md` |
| What APIs and data structures are allowed? | `api-contracts.md` + `architecture.md` (schema) |
| What has already happened? | `progress-tracker.md` |
| What decisions have been made? | `decisions.md` |
| What libraries should I use and how? | `library-docs.md` |
| What should the interface look like? | `ui-registry.md` + `ui-rules.md` + `ui-tokens.md` |
| What should I do if I don't know? | **Stop and ask.** |

If the AI cannot find the answer in these files, it should stop and ask — not guess.

---

## Project Architecture

LMS-PKG consists of three applications:

```
Mobile App (Flutter)          → Peserta (participants) — learning
Web Dashboard (Next.js)       → Instruktur + Admins — management
Backend API (NestJS)          → Business logic, data, authorization
```

The backend is the authoritative owner of all business logic. The mobile app and web dashboard are presentation layers that consume the API.

All three applications live in a monorepo under `/apps/`.

---

## Context Files

### Product Truth — what the system is supposed to do

| File | Purpose |
|---|---|
| `project-overview.md` | Product requirements, user roles, user flows, features in/out of scope |
| `build-plan.md` | Feature-level requirements (REQ-xxx), business rules, implementation tasks |

### Technical Truth — how the system is designed to work

| File | Purpose |
|---|---|
| `architecture.md` | System architecture, stack, repo structure, database schema, auth, API design |
| `api-contracts.md` | API endpoint definitions — the boundary between clients and backend |
| `decisions.md` | Architecture Decision Records — why things are built the way they are |
| `code-standards.md` | Implementation rules for all three apps, AI stop conditions |
| `library-docs.md` | How to use each library in this specific project |

### Visual Truth — what the interface should look like

| File | Purpose |
|---|---|
| `ui-registry.md` | Catalog of built components — check before building new ones |
| `ui-rules.md` | Visual design rules and patterns for both platforms |
| `ui-tokens.md` | Design token values (colors, typography, spacing, etc.) |

### Project State — what has actually happened

| File | Purpose |
|---|---|
| `progress-tracker.md` | Feature status, decisions log, migrations, issues, deviations |

---

## Vertical-Slice Development

Features are the primary unit of work — not application layers.

Instead of building "all backend endpoints" then "all mobile screens" then "all dashboard pages", each feature is implemented as a complete vertical slice:

```
Feature: Manajemen Kursus
    ↓
Requirements (REQ-COURSE-xxx) — defined in build-plan.md
    ↓
Business Rules — defined in build-plan.md
    ↓
Data Model — schema in architecture.md, migration in backend
    ↓
API Contract — defined in api-contracts.md
    ↓
Backend Implementation — NestJS module
    ↓
Mobile Implementation — Flutter screens (if Peserta is affected)
    ↓
Dashboard Implementation — Next.js pages (if Instruktur/Admin is affected)
    ↓
Tests
    ↓
Progress Tracker Updated
```

This ensures every feature is fully usable before moving to the next one.

---

## How to Start a New Feature

### Step 1: Read the Feature Plan

Open `build-plan.md` and find the feature section. Read:
- Requirements (REQ-xxx identifiers)
- Business rules
- Dependencies (are prerequisite features done?)
- Data changes
- API changes needed

If any requirement is unclear — stop and define it before proceeding.

### Step 2: Define the API Contract

Before any implementation, define the API endpoints in `api-contracts.md`:
- Endpoints, methods, paths
- Request bodies with validation constraints
- Response shapes
- Error responses
- Authorization requirements

The API contract is the agreement between backend and clients. Once defined, both sides can implement independently.

### Step 3: Implement the Backend

Build the NestJS module:
- Prisma schema changes and migration
- Module, controller, service, DTOs
- Authentication and authorization guards
- Input validation
- Error handling
- Tests

The backend must be working before client implementation begins.

### Step 4: Implement the Clients

With the API contract defined and backend working:
- **Mobile (Flutter):** Build screens, widgets, API service, state management
- **Dashboard (Next.js):** Build pages, components, API client integration

Each client implements only what the feature requires for its platform:
- Peserta features → Mobile
- Instruktur/Admin features → Dashboard
- Some features (like user profile) → Both

### Step 5: Verify and Record

After implementation:
1. Run the Definition of Done checklist from `build-plan.md`
2. Update `progress-tracker.md` with the feature status
3. Update `ui-registry.md` if new reusable components were created
4. Update `api-contracts.md` if the contract changed during implementation

---

## How Backend, Mobile, and Dashboard Interact

```
                    api-contracts.md
                    (source of truth)
                          │
            ┌─────────────┼─────────────┐
            │             │             │
       Mobile App    Backend API   Web Dashboard
       (consumes)    (implements)  (consumes)
```

### Rules

1. **API contracts are defined first** — before backend or client implementation
2. **Backend implements the contract** — if the contract needs to change, update the document first
3. **Clients consume the contract** — they build against what's defined, not what they wish existed
4. **Business logic lives only in the backend** — clients never duplicate validation, authorization, or business rules
5. **If a client needs something the contract doesn't provide** — flag it, update the contract, then implement

### What Clients Must NOT Do

- Implement business rules that belong to the backend
- Invent API fields that don't exist in the contract
- Assume database structure — only know what the API returns
- Validate beyond basic UX validation (required fields, format) — real validation is server-side
- Make architectural decisions without recording them in `decisions.md`

---

## How API Contracts Work

The `api-contracts.md` file defines every API endpoint:

```
Method + Path
Auth requirements
Request body with types and validation
Response body with types
Error responses with codes
Business rules that affect behavior
```

### Workflow

1. When planning a feature → define the contracts
2. When implementing backend → implement exactly what the contract says
3. When implementing clients → consume exactly what the contract says
4. When the contract needs to change → update the document first, then code

### Why This Matters

Without explicit contracts:
- The backend developer invents response fields
- The mobile developer invents request fields
- The web developer assumes a different response shape
- Everything breaks when they connect

With explicit contracts:
- Everyone builds against the same specification
- AI agents cannot invent APIs — they either find it in the contract or stop and ask

---

## How Progress is Tracked

`progress-tracker.md` is the single source of truth for project state.

### What Gets Tracked

- Feature status (completed, in progress, blocked, planned, cancelled, awaiting decision)
- Implementation status per layer (backend, mobile, dashboard)
- Architecture decisions made (quick reference — details in decisions.md)
- Pending decisions that need resolution
- Database migrations created
- API contract changes
- Known issues
- Important deviations from the plan

### Rules

- Update after every completed feature
- Update when a feature is blocked — document why
- Update when a decision is made
- Update when a migration is created
- If it's not in the tracker, assume the status is unknown

### Resuming After a Break

If development stopped for weeks or months:

1. Read `progress-tracker.md` — understand what has been done
2. Read `decisions.md` — understand what decisions are in place
3. Check `build-plan.md` — find the next feature
4. Check `api-contracts.md` — see what contracts exist
5. Proceed from where things left off

The AI must never guess what has been done. If the tracker says nothing about a feature, treat it as not started.

---

## How Architectural Decisions Work

Important decisions are recorded in `decisions.md` as Architecture Decision Records (ADRs).

### What Gets Recorded

- Technology choices (why NestJS, why Flutter, why PostgreSQL)
- Architectural patterns (modular monolith, not microservices)
- Authentication strategy (JWT, not sessions)
- Platform scope (mobile for students, web for admins)
- Any decision that affects multiple features or applications

### When to Record

Record a decision when:
- A choice affects the system architecture
- A choice affects multiple features
- A choice has significant trade-offs
- Someone might ask "why did we do it this way?" later

### Changing Decisions

If a decision needs to change:
1. Don't modify the original ADR
2. Create a new ADR that supersedes the old one
3. Mark the original as superseded
4. Implement the change only after recording the new decision

AI agents must never silently change an architectural decision. If the AI thinks a decision should change — it must flag it, explain the problem, propose the new decision, and wait for approval.

---

## When AI Must Stop and Ask

The AI must stop instead of guessing in these situations:

### Missing Requirements
- Business rule not defined in build-plan.md
- User behavior undefined for an edge case
- Acceptance criteria unclear

### Missing Contracts
- API endpoint not defined in api-contracts.md
- Response shape not documented
- Error handling for a specific case not specified

### Missing Design
- No screen design available
- Component behavior undefined
- New visual pattern needed

### Architecture Conflicts
- Implementation would contradict an existing ADR
- New infrastructure dependency needed
- Module boundary would be violated

### Cross-Application Issues
- Client needs logic that should be in the backend
- Mobile and web need different behavior not documented
- Data contract is ambiguous

**In every case:** identify the ambiguity, explain why it matters, present options if useful, and wait for a decision before continuing.

---

## Definition of Done

A feature is complete when all applicable items are satisfied:

```
[ ] Requirements implemented (all REQ-xxx for this feature)
[ ] Business rules enforced by backend
[ ] Database migration created (if data model changed)
[ ] API contract documented in api-contracts.md
[ ] Backend implementation completed
[ ] Authorization enforced
[ ] Input validation implemented
[ ] Mobile implementation completed (if applicable)
[ ] Dashboard implementation completed (if applicable)
[ ] Error handling implemented
[ ] Tests added
[ ] Existing tests still pass
[ ] Progress tracker updated
[ ] No unrelated changes introduced
```

---

## Practical Example: Starting a Development Session

### Morning: Pick up where you left off

```
1. Read progress-tracker.md
   → "Feature 1 (Akses Pengguna) backend completed, mobile in progress"

2. Read build-plan.md → Feature 1 → Mobile Tasks
   → Login screen and registration screen done
   → Profile view screen is next

3. Read api-contracts.md → User Endpoints
   → GET /api/users/me returns { id, email, fullName, phone, role }
   → PATCH /api/users/me accepts { fullName, phone }

4. Implement the profile view screen in Flutter
   → Uses the GET endpoint to display profile
   → Edit button opens edit screen using PATCH endpoint

5. Update progress-tracker.md
   → "Feature 1 mobile: login ✅, register ✅, profile view ✅, profile edit next"
```

### Encountering Ambiguity

```
While implementing profile edit, you realize:
"Should the user be able to change their email?"

1. Check project-overview.md → nothing about email change
2. Check build-plan.md → REQ-PROFILE-001 says "view and update" but doesn't list email as editable
3. Check api-contracts.md → PATCH /api/users/me doesn't include email

Decision needed:
→ Stop
→ Ask: "Should users be able to change their email? The contract doesn't include it."
→ Wait for answer
→ If yes: update contract, implement
→ If no: proceed without email editing
```

### End of Session

```
1. Update progress-tracker.md with what was completed
2. Add any notes or decisions made during the session
3. If new components were built — update ui-registry.md
4. If the API contract changed — verify api-contracts.md is current
```

---

## Requirement Traceability

Every requirement has a stable identifier:

```
REQ-AUTH-001       → User registration
REQ-COURSE-003     → Module management within courses
REQ-ASSESS-007     → Automatic assessment scoring
REQ-CERT-005       → Certificate PDF generation
```

These identifiers trace through:

```
Requirement (REQ-xxx in build-plan.md)
    ↓
Business Rule (in build-plan.md)
    ↓
Data Model (in architecture.md → Prisma schema)
    ↓
API Contract (in api-contracts.md)
    ↓
Implementation (in code)
    ↓
Test (verifying the requirement)
```

If code exists but no requirement traces to it — flag it. Code without a reason is a liability.

If a requirement exists but no implementation traces to it — the feature is incomplete.
