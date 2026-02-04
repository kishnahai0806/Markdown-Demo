# Architecture Decision Records (ADRs)

This document records the key architecture decisions for the **Campus Hub** MVP.

---

## ADR-001

**Title:** System roles & communication — Client–Server  
**Status:** Accepted

### Context
Campus Hub is a web app where students browse events, organizers submit events, and admins review/approve them. We need clear ownership of rules like event status transitions and permissions, without unnecessary operational complexity.

### Decision
Use a **Client–Server** architecture:
- **Client:** browser-based UI
- **Server:** a single backend API responsible for validation, auth, permissions, and event workflow
- **Database:** server-managed persistence

### Alternatives Considered
- **Event-driven architecture:** message broker + eventual consistency
- **Server-rendered only (full SSR):** minimal client logic

### Consequences
**Positive**
- Simple mental model and fewer moving parts for a small team
- Centralized enforcement of the event lifecycle rules (`submitted/approved/rejected`)

**Negative**
- Backend must stay well-structured to avoid becoming a “ball of mud”

---

## ADR-002

**Title:** Deployment & evolution — Modular monolith  
**Status:** Accepted

### Context
The MVP needs fast iteration, straightforward testing, and low ops overhead. Team size and expected traffic do not justify microservices complexity.

### Decision
Build and deploy as a **single application (monolith)** with **modular boundaries** internally (a “modular monolith”).

### Alternatives Considered
- **Microservices:** multiple deployables + service-to-service communication
- **Separate backend apps:** distinct services for discovery vs review

### Consequences
**Positive**
- One deploy pipeline, simpler debugging, lower cost
- Clear path to evolve by keeping internal modules isolated

**Negative**
- Requires discipline to keep module boundaries clean

---

## ADR-003

**Title:** Code organization — Feature-based (vertical slices) with layered subfolders  
**Status:** Accepted

### Context
ChatGPT recommended feature-based organization; Gemini recommended layered architecture. For maintainability in a small team, we want changes localized by feature *without* losing clarity of “where business logic lives.”

### Decision
Use **feature-based top-level modules** (vertical slices) with **layered subfolders inside each feature**.

Example:
- `submission/` → `controller/`, `service/`, `repository/`, `model/`
- `discovery/` → `controller/`, `service/`, `repository/`, `model/`
- `review/` → `controller/`, `service/`, `repository/`, `model/`
- `accounts/` → `controller/`, `service/`, `repository/`, `model/`

### Alternatives Considered
- **Pure layered:** `controllers/`, `services/`, `repositories/` globally
- **Pure feature-based:** features only, no layer separation

### Consequences
**Positive**
- Feature changes don’t ripple across unrelated areas
- Still easy to find business rules vs HTTP wiring

**Negative**
- Must avoid duplicated shared logic; use a small `shared/` or `common/` module when needed

---

## ADR-004

**Title:** Data & state ownership — Single relational database  
**Status:** Accepted

### Context
The MVP relies on event lifecycle state, search/filter queries, and relational connections (events ↔ users ↔ bookmarks ↔ reviews).

### Decision
Use a **single relational database** as the system of record.

### Alternatives Considered
- **Database per service:** microservices pattern that creates data silos
- **NoSQL-first:** more work for relational queries and integrity constraints

### Consequences
**Positive**
- Simple, consistent queries for discovery (filters, sorting)
- Easier migrations, backups, and data integrity

**Negative**
- Need migration discipline as the schema evolves

---

## ADR-005

**Title:** Interaction model — Synchronous request/response  
**Status:** Accepted

### Context
Users need immediate feedback for actions like submitting an event, approving/rejecting, and searching. The MVP doesn’t include heavy processing workloads.

### Decision
Use **synchronous HTTP request/response** for user-facing flows.

### Alternatives Considered
- **Async/queued workflows:** background jobs and eventual consistency for core actions

### Consequences
**Positive**
- Immediate validation errors and success confirmations
- Simple API + UX for CRUD workflows

**Negative**
- Background jobs may still be needed later for non-user-facing tasks (e.g., email reminders)
