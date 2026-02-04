Frontend (Client)

React + Vite (fast dev/build, simple SPA)

React Router (routes like /events, /submit, /admin/review)

Tailwind CSS (quick consistent UI)

TanStack Query (fetching + caching + invalidation after submit/approve)

Optional: Zod (form validation on the client)

Backend (Server / Modular Monolith)

Java 21 + Spring Boot

Spring Web (REST) for synchronous request/response

Spring Security for login + role-based access (USER / ORGANIZER / ADMIN)

Bean Validation (Jakarta Validation) for input validation (dates, required fields)

OpenAPI/Swagger (springdoc) for API docs

Database (Single source of truth)

PostgreSQL (events/users/bookmarks/approvals fit relational well)

Flyway (or Liquibase) for migrations

Auth approach (simple + matches roles)

JWT access token + refresh token (or cookie-based sessions if you want simplest server control)

Store roles in DB, enforce authorization in backend (RBAC + ownership checks)

Local Dev

Docker Compose for Postgres (+ optional pgAdmin)

.env for local config (DB URL, JWT secret)

Deploy (low-ops MVP)

Render / Railway / Fly.io for Spring Boot web service

Managed Postgres on the same platform

Vercel / Netlify for the React frontend

CI/CD

GitHub Actions: run tests → build → deploy (separate workflows for frontend/backend if split repos, or one if monorepo)

Testing

Backend: JUnit 5 + Mockito + Spring Boot Test

Frontend: Vitest + React Testing Library

Logging / Monitoring (lightweight but real)

Backend logging: SLF4J/Logback, structured JSON logs if you want

Error tracking: Sentry (optional but great for MVP)
