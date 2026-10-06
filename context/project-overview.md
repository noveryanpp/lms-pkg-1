# Project Overview

## About the Project

LMS-PKG is a Learning Management System designed to replace in-person training with a digital platform. The system allows organizations to manage training programs, distribute learning materials, evaluate participants, and track learning progress — all from a centralized platform.

The architecture consists of three applications: a Flutter mobile app for participants (the primary learning platform), a Next.js web dashboard for instructors and administrators, and a NestJS backend API that owns all business logic and data access.

Participants follow courses, complete assessments, track their progress, and receive certifications — all through the mobile app. Instructors create and manage course content, build assessments, and monitor participant progress — through the web dashboard. Administrators manage users, courses, certifications, and platform operations — through the web dashboard.

---

## The Problem It Solves

Organizations currently require participants to attend training at specific physical locations. This creates high travel costs, scheduling conflicts, and limited accessibility for participants in remote areas. As training programs scale, manual administration of participants, materials, assessments, and certifications becomes increasingly difficult.

LMS-PKG centralizes all training operations into a single platform — making training accessible from anywhere, reducing operational costs, and enabling the organization to scale training programs efficiently.

---

## Target Users

### Peserta (Participant)

Primary user of the platform. Enrolls in courses, accesses learning materials, completes assessments, tracks learning progress, and obtains certifications after meeting requirements.

**Primary platform:** Mobile App

### Instruktur (Instructor)

Creates and manages learning content. Uploads course materials, builds assessments, publishes courses, and monitors participant progress.

**Primary platform:** Web Dashboard

### Administrator Platform (Platform Admin)

Manages overall platform operations. Handles user management, course management, activity monitoring, and reporting.

**Primary platform:** Web Dashboard

### Administrator Lembaga (Certification Admin)

Manages certification operations. Handles certification processes, participant certification data, assessment administration, and certificate issuance.

**Primary platform:** Web Dashboard

---

## User Flows

### Peserta (Mobile App)

```
Register → Login → Browse Courses → Enroll → Learn (Materials + Video) →
Complete Assessments → Track Progress → Pass → Receive Certificate
```

### Instruktur (Web Dashboard)

```
Login → Create Course → Upload Materials → Build Assessments →
Publish Course → Monitor Participant Progress
```

### Administrator Platform (Web Dashboard)

```
Login → Manage Users → Manage Courses → Monitor Activity → View Reports
```

### Administrator Lembaga (Web Dashboard)

```
Login → View Participant Data → Verify Requirements →
Manage Certification Status → Issue Certificates
```

---

## Applications

| Application | Technology | Primary Users | Purpose |
|---|---|---|---|
| Mobile App | Flutter + Dart | Peserta | Learning platform — courses, assessments, progress, certificates |
| Web Dashboard | Next.js + TypeScript | Instruktur, Admin Platform, Admin Lembaga | Management — content, users, monitoring, certification |
| Backend API | NestJS + TypeScript | All (via apps) | Business logic, data access, authorization, API |

### Platform Boundaries

- **Peserta** use the Mobile App exclusively for learning activities
- **Instruktur and Administrators** use the Web Dashboard for management
- Both applications consume the same Backend API
- The Backend API is the single source of truth for all business logic

---

## Phase 1 — Core Platform

Phase 1 builds the foundation: user access, course management, assessment, certification, dashboards, and administration.

### Feature 1: Akses Pengguna (User Access)

Registration, login, user profiles, and role-based access control.

**Scope:**
- Registration and login
- User profile management
- Role-based access (Peserta, Instruktur, Admin Platform, Admin Lembaga)
- JWT authentication with refresh tokens

### Feature 2: Manajemen Kursus (Course Management)

Course creation, content structuring, material management, and course publishing.

**Scope:**
- Course listing and details
- Course structure (modules, sections)
- Learning materials (text, video, documents)
- Course management (create, edit, publish, archive)

### Feature 3: Asesmen & Evaluasi (Assessment & Evaluation)

Pre-tests, post-tests, quizzes, evaluation results, and assessment history.

**Scope:**
- Pre-test and post-test
- Quizzes per module/section
- Evaluation results and scoring
- Assessment history and review

### Feature 4: Sertifikasi (Certification)

Certification eligibility, certificate generation, and certification history.

**Scope:**
- Participant certification data
- Digital certificate issuance
- Certification history and status tracking

### Feature 5: Dashboard

Role-specific dashboards showing relevant information and activity summaries.

**Peserta dashboard (Mobile):**
- Enrolled courses
- Learning progress
- Assessment results
- Certificates

**Instruktur / Admin dashboard (Web):**
- Course and participant data
- Learning activity summary
- Assessment results overview

### Feature 6: Administrasi (Administration)

Platform management functions for administrators.

**Scope:**
- User management
- Course and material management
- Assessment management
- Certification data management
- Platform activity monitoring
- Basic data and reporting

---

## Phase 2 — Growth (Future)

Planned features for post-Phase 1. Scope is indicative and subject to evaluation.

- Push Notifications (Firebase Cloud Messaging)
- Course Rating & Review
- Discussion Rooms

---

## Phase 3 — Scale & Expansion (Future)

Planned features for long-term growth. Scope is indicative.

- Membership system
- Course packages
- Multi-language support
- External system integrations

---

## Features Out of Scope (Phase 1)

- Push notifications — Phase 2
- Course rating and reviews — Phase 2
- Discussion rooms — Phase 2
- Membership or subscription system — Phase 3
- Course packages or bundling — Phase 3
- Multi-language support — Phase 3
- External system integrations — Phase 3
- Payment processing
- Offline mode for mobile app
- Real-time chat or messaging
- Gamification (badges, leaderboards)
- Social features (following, sharing)
- Content marketplace
- White-labeling
- Public API for third parties
- Email notifications (Phase 1)
- Live video or webinar integration
- AI-powered content generation
- Analytics or BI dashboard beyond basic reporting

---

## Data Architecture

### Core Entities

The primary data entities for Phase 1:

- **User** — all user accounts with role assignments
- **Course** — training programs with metadata and status
- **Module** — structural units within a course
- **Material** — learning content (text, video, documents) within modules
- **Assessment** — tests and quizzes linked to courses or modules
- **Question** — individual questions within assessments
- **Attempt** — participant's assessment submission with answers and score
- **Enrollment** — participant's registration in a course
- **Progress** — participant's learning progress tracking
- **Certificate** — issued certificates linked to participant and course

### Data Ownership

- All data is owned and managed by the Backend API
- Mobile App and Web Dashboard read/write data exclusively through the API
- No local business logic databases in the client applications
- File storage (materials, certificates) uses S3-compatible object storage

---

## Success Criteria

- Peserta can register, browse courses, enroll, learn, complete assessments, and receive certificates through the mobile app
- Instruktur can create courses, upload materials, build assessments, and monitor progress through the web dashboard
- Administrators can manage users, courses, certifications, and view reports through the web dashboard
- Role-based access control correctly restricts features per user role
- Assessment scoring is accurate and results are immediately available
- Certificates are generated and accessible after completion requirements are met
- The system handles concurrent users without performance degradation
- All three applications consume the same API consistently
- Data integrity is maintained across all operations
