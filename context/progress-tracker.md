# Progress Tracker

Update this file after every completed feature, decision, or significant change. Any AI agent reading this should immediately know what is done, what is in progress, what is blocked, and what decisions have been made — even if development stopped months ago.

This is the authoritative source for project state. If something is not recorded here, treat its status as unknown — never assume it is complete.

---

## Current Status

**Phase:** Phase 1 — Core Platform
**Last completed:** Initial context setup and architecture definition
**In progress:** —
**Blocked:** —
**Next:** Feature 1 — Akses Pengguna (User Access)

---

## Feature Status

### Phase 1 — Core Platform

| Feature | Status | Backend | Mobile | Dashboard | Notes |
|---|---|---|---|---|---|
| 1. Akses Pengguna | 🔵 Planned | — | — | — | Foundation — must be first |
| 2. Manajemen Kursus | 🔵 Planned | — | — | — | Depends on Feature 1 |
| 3. Asesmen & Evaluasi | 🔵 Planned | — | — | — | Depends on Features 1, 2 |
| 4. Sertifikasi | 🔵 Planned | — | — | — | Depends on Features 1, 2, 3 |
| 5. Dashboard | 🔵 Planned | — | — | — | Depends on all data features |
| 6. Administrasi | 🔵 Planned | — | — | — | Web-only, no mobile tasks |

### Status Legend

- 🟢 **Completed** — fully implemented, tested, working
- 🟡 **In Progress** — actively being implemented
- 🔴 **Blocked** — cannot proceed, reason documented below
- 🔵 **Planned** — defined in build-plan.md, not started
- ⚫ **Cancelled** — no longer needed, reason documented
- 🟠 **Awaiting Decision** — blocked by a pending decision

---

## Architecture Decisions Log

Decisions are recorded in `decisions.md` with full context. This section provides a quick reference.

| ADR | Decision | Date |
|---|---|---|
| ADR-001 | Modular monolith for backend | 2026-10-06 |
| ADR-002 | Three-application architecture | 2026-10-06 |
| ADR-003 | JWT + Refresh Token authentication | 2026-10-06 |
| ADR-004 | PostgreSQL + Prisma ORM | 2026-10-06 |
| ADR-005 | S3-compatible object storage | 2026-10-06 |
| ADR-006 | Peserta mobile-only, Admin/Instruktur web-only | 2026-10-06 |
| ADR-007 | Monorepo structure | 2026-10-06 |

---

## Pending Decisions

| Topic | Context | Options | Status |
|---|---|---|---|
| Flutter state management | Need to choose Riverpod, Bloc, or Provider for mobile app state | Riverpod / Bloc / Provider | 🟠 Awaiting |
| Dashboard styling | Need to choose CSS/component approach for web dashboard | TailwindCSS / CSS Modules / other | 🟠 Awaiting |
| Dashboard UI library | Need to choose component library for web dashboard | shadcn/ui / Ant Design / custom | 🟠 Awaiting |
| Certificate PDF template | Need design for certificate PDF generation | — | 🟠 Awaiting |
| Certificate number format | Format for unique certificate numbers | CERT-{YEAR}-{SEQ} / other | 🟠 Awaiting |

---

## Migrations

| Migration Name | Feature | Date | Description |
|---|---|---|---|
| _Entries added as migrations are created_ | | | |

---

## API Changes

| Endpoint | Feature | Date | Change |
|---|---|---|---|
| _Entries added as API contracts are created or modified_ | | | |

---

## Known Issues

_No known issues yet._

---

## Important Deviations

_Any implementation that deviates from the context files is documented here with the reason._

---

## Notes

_Add notes here as the build progresses — workarounds, patterns, anything that differs from the context files._
