# Tech Stack Decision Records

Once the architecture is decided, next step is to choose the technology stack (tech stack)

A tech stack is the combination of programming languages, frameworks, libraries, databases, servers, and tools used to build and run a single software application.

From a chosen architecture, choose between popular tech stacks.
Use the following dimensions to support your decision:
1) AI Support Strength
2) Popularity & Community Answers
3) Ecosystem Maturity
4) Deployment Simplicity
5) Path to Next Architecture

---

## TSDR-000

**Title:** Overall tech stack direction — React + Spring Boot + PostgreSQL  
**Status:** Accepted

### Context
The Campus Hub MVP uses a **client–server** architecture with a **modular monolith**, **single SQL database**, and **synchronous flows**. We need a stack that is:
- easy to build and debug in a small team,
- well-supported by AI tools and documentation,
- easy to deploy on low-ops platforms,
- and has a clear upgrade path if we later move toward a more scalable architecture.

### Decision
Choose a mainstream, well-documented stack:
- **Frontend:** React + Vite
- **Backend:** Java 21 + Spring Boot (REST)
- **Database:** PostgreSQL

### Alternatives Considered
- **Full TypeScript stack:** React/Next.js + Node.js (Express/Nest) + Postgres
- **Python stack:** React + FastAPI + Postgres
- **Server-rendered only:** Spring MVC templates (minimal SPA)

### Consequences
**Positive**
- Strong AI support and abundant examples for all layers (React, Spring Boot, Postgres)
- Mature ecosystems with lots of community Q&A and libraries
- Simple MVP deployment on managed platforms; clear path to “next architecture” (containers/AWS/ECS later)

**Negative**
- Using Java/Spring for an MVP can feel heavier than Node/FastAPI if the team only needs minimal features
- Requires learning Spring conventions (configuration, dependency injection, security)

---

## TSDR-001

**Title:** Frontend framework — React + Vite  
**Status:** Accepted

### Context
We need a modern UI for event discovery and submissions with fast local development and a common industry stack. By the decision dimensions:
- **AI Support Strength / Popularity / Maturity:** React has the largest ecosystem and examples.
- **Deployment Simplicity:** static build works well on CDNs.
- **Path to Next Architecture:** can evolve to SSR (Next.js) later if needed.

### Decision
Use **React** with **Vite**.

### Alternatives Considered
- Next.js (SSR/fullstack)
- Vue / Svelte
- Server-rendered templates only

### Consequences
**Positive**
- Fast dev server and build times
- Large ecosystem and strong community/AI support

**Negative**
- Requires client-side routing and API integration patterns

---

## TSDR-002

**Title:** Styling — Tailwind CSS  
**Status:** Accepted

### Context
The MVP needs a clean, consistent design without spending lots of time on custom CSS architecture. By the decision dimensions:
- **AI Support / Popularity:** lots of examples and common patterns.
- **Deployment Simplicity:** no runtime dependency, just builds to CSS.
- **Path to Next Architecture:** stays valid whether SPA or SSR.

### Decision
Use **Tailwind CSS**.

### Alternatives Considered
- CSS Modules
- styled-components
- Heavy component frameworks (MUI/Chakra)

### Consequences
**Positive**
- Rapid UI iteration and consistent styling
- Less long-term CSS drift

**Negative**
- Team should align on reusable component patterns to avoid messy markup

---

## TSDR-003

**Title:** Server-state fetching — TanStack Query  
**Status:** Accepted

### Context
The UI needs caching, loading states, retries, and easy refresh after mutations (submit/approve). By the decision dimensions:
- **Popularity / Maturity:** widely used in React apps.
- **AI Support:** many code examples for caching/invalidation.
- **Path to Next Architecture:** continues to work if the backend scales out.

### Decision
Use **TanStack Query** for API data fetching and caching.

### Alternatives Considered
- Redux async flows
- SWR
- Manual fetch + local state

### Consequences
**Positive**
- Clean cache invalidation model (great for “submit → see it update” flows)
- Less boilerplate for async state

**Negative**
- Requires consistent query key conventions across the team

---

## TSDR-004

**Title:** Backend — Spring Boot REST API  
**Status:** Accepted

### Context
We need a single backend to enforce event status rules, validation, and RBAC permissions, matching the **modular monolith** decision. By the decision dimensions:
- **Ecosystem Maturity:** Spring is mature with strong security/validation support.
- **Popularity / AI Support:** lots of community answers and examples.
- **Deployment Simplicity:** runs as one service; easy to containerize later.
- **Path to Next Architecture:** can move to containers/AWS/ECS without rewriting.

### Decision
Use **Java 21 + Spring Boot** with:
- **Spring Web** (REST)
- **Spring Security** (auth + RBAC)
- **Jakarta Bean Validation** (request validation)
- **springdoc-openapi** (Swagger/OpenAPI docs)

### Alternatives Considered
- Node.js (Express/Nest)
- Python (FastAPI)
- Serverless-only backend

### Consequences
**Positive**
- Strong ecosystem for security, validation, and clean layering
- Fits modular monolith structure well

**Negative**
- Slightly heavier than minimal frameworks for an MVP (more setup and conventions)

---

## TSDR-005

**Title:** Database — PostgreSQL + migrations  
**Status:** Accepted

### Context
Event discovery needs filtering/sorting and relational integrity (events, users, bookmarks, approvals). By the decision dimensions:
- **Maturity:** Postgres is stable and widely supported.
- **Popularity / AI Support:** tons of examples for queries and schema design.
- **Deployment Simplicity:** managed Postgres is available on most platforms.
- **Path to Next Architecture:** works with read replicas and scaling patterns later.

### Decision
Use **PostgreSQL** with **Flyway** (or Liquibase) for migrations.

### Alternatives Considered
- MySQL
- NoSQL (DynamoDB/document DB)

### Consequences
**Positive**
- Excellent support for relational queries + constraints
- Predictable schema changes via migrations

**Negative**
- Requires consistent migration practices across environments

---

## TSDR-006

**Title:** Authentication — Spring Security with JWT (or cookie sessions) + RBAC  
**Status:** Accepted

### Context
We need simple authentication and clear authorization rules for roles: `USER`, `ORGANIZER`, `ADMIN`. By the decision dimensions:
- **Maturity:** Spring Security is a standard solution.
- **AI Support / Popularity:** many guides and examples.
- **Path to Next Architecture:** easy to upgrade to OAuth/SSO later.

### Decision
Use **Spring Security** with:
- **Option A:** JWT access + refresh tokens *(common for SPAs)*
- **Option B:** cookie-based sessions *(simplest server control)*
And enforce:
- **RBAC roles**
- **Ownership checks** (organizers manage only their own events)

### Alternatives Considered
- OAuth-only from day one
- Custom auth without a security framework

### Consequences
**Positive**
- Clear permissions model for review workflow
- Flexible to upgrade to campus SSO later

**Negative**
- Token/session handling must be implemented carefully (security)

---

## TSDR-007

**Title:** Deployment — Low-ops hosting + managed Postgres  
**Status:** Proposed

### Context
The MVP should be easy to deploy and maintain with limited operational resources. By the decision dimensions:
- **Deployment Simplicity:** managed services reduce ops.
- **Path to Next Architecture:** can move to AWS/container platforms later.

### Decision
Deploy:
- **Backend:** Render / Railway / Fly.io (managed web service)
- **Database:** managed Postgres on the same platform
- **Frontend:** Vercel / Netlify (static hosting/CDN)

### Alternatives Considered
- AWS ECS/Fargate + RDS
- Single VPS + Docker Compose

### Consequences
**Positive**
- Minimal ops for MVP, easy iteration
- Clear path to move to AWS later if needed

**Negative**
- Platform limits/costs may drive future migration

---

## TSDR-008

**Title:** CI/CD — GitHub Actions  
**Status:** Accepted

### Context
We want repeatable builds/tests and safe deployments. By the decision dimensions:
- **Popularity / AI Support:** many templates and examples.
- **Deployment Simplicity:** integrates directly with GitHub repos.
- **Path to Next Architecture:** can later add security scans, container builds, multi-env deploys.

### Decision
Use **GitHub Actions** to:
- run unit tests
- build frontend/backend
- deploy on merges to `main`

### Alternatives Considered
- Manual deploys
- Third-party CI systems only

### Consequences
**Positive**
- Consistent automation and fewer “works on my machine” issues
- Easy to extend later (linting, security scans)

**Negative**
- Requires secrets management for deploy credentials
