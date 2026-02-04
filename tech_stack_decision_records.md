# Tech Stack Decision Records

This document records the technology choices that support the Campus Hub MVP architecture:
**client–server**, **modular monolith**, **single SQL DB**, **synchronous flows**, and **feature-based modules with layered subfolders**.

**Format:** Context → Decision → Alternatives → Consequences

---

## TSDR-001: Frontend framework — React + Vite
**Status:** Accepted  
**Date:** 2026-02-04

### Context
We need a modern UI for event discovery and submissions with fast local development and a common industry stack.

### Decision
Use **React** with **Vite**.

### Alternatives considered
- Next.js (SSR/fullstack)
- Vue / Svelte
- Server-rendered templates only

### Consequences
- ✅ Fast dev server and build times
- ✅ Large ecosystem and common hiring value
- ⚠️ Requires client-side routing and API integration patterns

---

## TSDR-002: Styling — Tailwind CSS
**Status:** Accepted  
**Date:** 2026-02-04

### Context
The MVP needs a clean, consistent design without spending lots of time on custom CSS architecture.

### Decision
Use **Tailwind CSS**.

### Alternatives considered
- CSS Modules
- styled-components
- Heavy component frameworks (MUI/Chakra)

### Consequences
- ✅ Rapid UI iteration and consistent styling
- ✅ Less long-term CSS drift
- ⚠️ Team should align on reusable component patterns

---

## TSDR-003: Server-state fetching — TanStack Query
**Status:** Accepted  
**Date:** 2026-02-04

### Context
The UI needs caching, loading states, retries, and easy refresh after mutations (submit/approve).

### Decision
Use **TanStack Query** for API data fetching and caching.

### Alternatives considered
- Redux async flows
- SWR
- Manual fetch + local state

### Consequences
- ✅ Clean cache invalidation model
- ✅ Less boilerplate for async state
- ⚠️ Requires consistent query key conventions

---

## TSDR-004: Backend — Spring Boot REST API
**Status:** Accepted  
**Date:** 2026-02-04

### Context
We need a single backend to enforce event status rules, validation, and RBAC permissions, matching the modular monolith decision.

### Decision
Use **Java 21 + Spring Boot** with:
- **Spring Web** (REST)
- **Spring Security** (auth + RBAC)
- **Jakarta Bean Validation** (request validation)
- **springdoc-openapi** (Swagger/OpenAPI docs)

### Alternatives considered
- Node.js (Express/Nest)
- Python (FastAPI)
- Serverless-only backend

### Consequences
- ✅ Strong ecosystem for security, validation, and clean layering
- ✅ Fits modular monolith structure well
- ⚠️ Slightly heavier than minimal frameworks for an MVP

---

## TSDR-005: Database — PostgreSQL + migrations
**Status:** Accepted  
**Date:** 2026-02-04

### Context
Event discovery needs filtering/sorting and relational integrity (events, users, bookmarks, approvals).

### Decision
Use **PostgreSQL** with **Flyway** (or Liquibase) for migrations.

### Alternatives considered
- MySQL
- NoSQL (DynamoDB/document DB)

### Consequences
- ✅ Excellent support for relational queries + constraints
- ✅ Predictable schema changes via migrations
- ⚠️ Requires consistent migration practices across environments

---

## TSDR-006: Authentication — JWT (or cookie sessions) with RBAC
**Status:** Accepted  
**Date:** 2026-02-04

### Context
We need simple authentication and clear authorization rules for roles: `USER`, `ORGANIZER`, `ADMIN`.

### Decision
Use **Spring Security** with:
- **Option A:** JWT access + refresh tokens *(common for SPAs)*
- **Option B:** cookie-based sessions *(simplest server control)*
And enforce:
- **RBAC roles**
- **Ownership checks** (organizers manage only their own events)

### Alternatives considered
- OAuth-only from day one
- Custom auth without a security framework

### Consequences
- ✅ Clear permissions model for review workflow
- ✅ Flexible to upgrade to campus SSO later
- ⚠️ Token/session handling must be implemented carefully (security)

---

## TSDR-007: Deployment — Low-ops hosting + managed Postgres
**Status:** Proposed  
**Date:** 2026-02-04

### Context
The MVP should be easy to deploy and maintain with limited operational resources.

### Decision
Deploy:
- **Backend:** Render / Railway / Fly.io (managed web service)
- **Database:** managed Postgres on the same platform
- **Frontend:** Vercel / Netlify (static hosting/CDN)

### Alternatives considered
- AWS ECS/Fargate + RDS
- Single VPS + Docker Compose

### Consequences
- ✅ Minimal ops for MVP, easy iteration
- ✅ Clear path to move to AWS later if needed
- ⚠️ Platform limits/costs may drive future migration

---

## TSDR-008: CI/CD — GitHub Actions
**Status:** Accepted  
**Date:** 2026-02-04

### Context
We want repeatable builds/tests and safe deployments.

### Decision
Use **GitHub Actions** to:
- run unit tests
- build frontend/backend
- deploy on merges to `main`

### Alternatives considered
- Manual deploys
- Third-party CI systems only

### Consequences
- ✅ Consistent automation and fewer “works on my machine” issues
- ✅ Easy to extend later (linting, security scans)
- ⚠️ Requires secrets management for deploy credentials
