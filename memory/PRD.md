# Institute Enquiry CRM — PRD

## Original problem statement
Full-stack Institute Enquiry Registration Web Application with:
- Roles: Super Admin, Admin, Reception, Counsellor, Faculty
- Modules: Auth (JWT + forgot/change pw), Enquiry registration & CRUD, Follow-up management, Admission conversion, Counsellor & Admin dashboards with charts, Reports, Settings (Courses/Batches/Sources/Users).

## User choices
- Tech: **FastAPI + SQLite (SQLAlchemy) + React + Material UI v9 + Chart.js**
- Auth: JWT (Bearer token in localStorage)
- Email notifications: skipped (in-app only)
- Scope: **Core-first** — Auth, Roles, Enquiry CRUD, Follow-ups, Dashboards, Reports basic, Settings CRUD
- Design: agent-decided ("Ivy Modern" — deep forest green primary, terracotta accents, Outfit + Cabinet Grotesk typography, dark sidebar / light content)

## Architecture
- Backend `/app/backend/` — FastAPI, SQLAlchemy sync, SQLite (`/app/backend/institute.db`)
  - `server.py` (single-file app with all routes), `models.py`, `schemas.py`, `auth.py`, `database.py`, `.env`
  - Seeded on startup: 5 users, 5 courses, 7 lead sources, 3 batches, 5 sample enquiries
- Frontend `/app/frontend/src/`
  - `App.js` router, `AuthProvider` context, protected routes with role guards
  - Pages: `Login`, `ForgotPassword`, `ChangePassword`, `Dashboard`, `EnquiryList`, `EnquiryForm`, `EnquiryDetail`, `FollowUps`, `Admissions`, `Reports`, `Settings`
  - Layout: `AppLayout` (dark sidebar + top navbar + avatar menu)
  - `theme.js` custom MUI v9 theme

## User personas
- **Super Admin / Admin** — full system access, users/settings, all reports
- **Reception** — register new enquiries, view lists, cannot manage settings/users
- **Counsellor** — see own enquiries, add follow-ups, convert admissions
- **Faculty** — read-only enquiries and admissions

## What's been implemented (2026-01-01)
- JWT auth (login, /me, change-password, forgot-password); role-based route + sidebar guards
- 5 seeded default accounts (all pw `Admin@123`)
- Enquiry model with 30+ fields across Personal/Academic/Course/Lead/Counselling sections
- Enquiry list with search, status filter, pagination, CSV export, print, duplicate mobile detection
- Enquiry detail page with full record + follow-up history
- Follow-ups (unlimited per enquiry) with status/next-date update propagation
- Admission conversion (auto admission number, prevents double-admit)
- Dashboards: 8 stat cards + 5 charts (monthly enquiries, admission trend, course pie, lead source doughnut, counsellor bar)
- Reports summary API + basic UI with date range + CSV export per section
- Settings: CRUD for Courses, Batches, Lead Sources, Users
- Responsive MUI v9 layout, Outfit + Cabinet Grotesk fonts, custom "Ivy Modern" theme
- Verified: backend 27/27 (iteration_1), frontend regressions all resolved (iteration_2, ~95%)

## Prioritized backlog
### P1
- File uploads (Photo, Aadhaar, Marksheet) — model fields exist, upload endpoint not wired
- Email notifications on follow-up reminders (SendGrid/Resend)
- Excel + PDF export (currently CSV only)
- Global top-navbar search
- Browser Push notifications

### P2
- Institute Profile + Academic Session settings tabs
- Activity Log timeline
- Advanced reports (Daily/Weekly/Monthly presets)
- Dark mode toggle (theme scaffolded)
- Bulk actions (assign counsellor, change status, delete)

### P3
- Multi-language, multi-branch, WhatsApp Cloud API integration, SMS reminders

## Next tasks
1. Add file uploads (photo/aadhaar/marksheet) with Django-media-style storage.
2. Wire follow-up reminder emails once user shares SendGrid/Resend key.
3. Add PDF/Excel exports.
4. Dark-mode toggle.
