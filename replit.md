# Student Portal

A full-stack, production-ready Student Portal with three roles (Admin, Faculty, Student), JWT authentication, and complete academic management features.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080, proxied at `/api`)
- `pnpm --filter @workspace/scripts run seed` — seed the database with sample data
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL`, `SESSION_SECRET`

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite (Tailwind v4, shadcn/ui, Wouter, TanStack Query)
- API: Express 5
- DB: PostgreSQL + Drizzle ORM (13 schema tables)
- Auth: bcryptjs + jsonwebtoken (access 15m, refresh 7d)
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (OpenAPI → React Query hooks + Zod schemas)

## Where things live

- `artifacts/student-portal/` — React + Vite frontend
- `artifacts/api-server/` — Express 5 API server
- `lib/db/` — Drizzle ORM schema + pool
- `lib/api-client-react/` — Generated React Query hooks + custom-fetch (with JWT auto-inject)
- `lib/api-zod/` — Generated Zod validation schemas
- `lib/api-spec/` — OpenAPI spec (source of truth for API contract)
- `scripts/src/seed.ts` — Database seed script (run from api-server context)

## Architecture decisions

- JWT stored in `localStorage` as `token`; custom-fetch auto-injects it via `setAuthTokenGetter`
- Role-based routing in frontend: admin→`/admin/*`, faculty→`/faculty/*`, student→`/dashboard`
- All API routes under `/api/` prefix; frontend served at `/`
- DB lib is composite (emits declarations); artifact packages are leaves (no emit)
- Seed script lives in `artifacts/api-server/src/seed.ts` and runs with the api-server's node_modules (has drizzle-orm)

## Product

- **Admin**: Full management of students, faculty, departments, subjects, classes, notices, announcements, timetable, marks + dashboard with institution stats
- **Faculty**: Dashboard with teaching overview, mark attendance per class, create/manage assignments & view submissions, enter/edit student marks
- **Student**: Dashboard with academic overview, view attendance by subject, submit assignments, view timetable, marks sheet, notices & announcements, edit profile

## Login credentials (after seeding)

| Role    | Email                   | Password     |
|---------|-------------------------|--------------|
| Admin   | admin@portal.edu        | admin123     |
| Faculty | faculty@portal.edu      | faculty123   |
| Student | student@portal.edu      | student123   |

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Run `pnpm run typecheck:libs` before running leaf artifact typechecks after any `lib/*` change
- The seed script must be run from `artifacts/api-server/` context (needs drizzle-orm in scope): `cd artifacts/api-server && /path/to/tsx src/seed.ts`
- DB schema enums: `userRoleEnum` (admin/faculty/student), `attendanceStatusEnum` (present/absent/late), `priorityEnum` (low/medium/high), `dayEnum` (Monday–Saturday)

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
