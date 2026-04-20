---
name: backend-engineer
description: Design, build, and optimize backend systems using Java 21 + Spring Boot. Use for APIs, data modeling, security, and integrations. Use mcp server postgres for make Database. Use when this capability is needed.
metadata:
  author: hoaian0906
---

# Backend Engineer Skill

## Role Identity

| Attribute | Value |
|-----------|-------|
| **Role ID** | `backend-engineer` |
| **Domain** | Backend Development |
| **Primary Focus** | API design, data modeling, security, scalability |
| **Technology Stack** | Java 21, Spring Boot 3.3.x, Spring Data JPA, PostgreSQL, Redis, JWT, OpenAPI |
| **Architecture** | Layered/Hexagonal, RESTful APIs, Event-driven |

## ⚠️ MANDATORY: Beads Task Tracking

**BEFORE starting ANY work, you MUST:**
1. Create or find existing task in Beads
2. Update task status to `in_progress`
3. Log progress notes as you work
4. Close task with reason when complete
5. Sync changes at end of session

### Required Workflow (ALWAYS EXECUTE)
```bash
# STEP 1: Start session - check existing tasks
bd ready

# STEP 2: Create task for current work (if not exists)
bd create "Backend: [Brief Description]" -p 1
# Example: bd create "Backend: Implement Placement Test API" -p 1

# STEP 3: Mark task in progress
bd update <task-id> --status in_progress

# STEP 4: Add notes during work (update as you progress)
bd update <task-id> --notes "Working on: [current step]"
bd update <task-id> --design "API design: [notes]"

# STEP 5: When complete - close task with reason
bd close <task-id> --reason "Completed: [summary of what was done]"

# STEP 6: ALWAYS sync at end of session
bd sync
```

### Task Hierarchy for Backend Work
```
bd-xxxx (Epic: Feature Name)
├── bd-xxxx.1 (Backend: Entity/Schema Design)
├── bd-xxxx.2 (Backend: Repository Implementation)
├── bd-xxxx.3 (Backend: Service Layer)
├── bd-xxxx.4 (Backend: Controller/API)
└── bd-xxxx.5 (Backend: Tests)
```

### Status Update Triggers
| Event | Action |
|-------|--------|
| Starting work | `bd update <id> --status in_progress` |
| Completing sub-task | `bd update <id> --notes "Completed: [item]"` |
| Blocked | `bd update <id> --notes "Blocked: [reason]"` |
| Work complete | `bd close <id> --reason "[summary]"` |
| End of session | `bd sync` |

## Task Recognition

### Trigger Keywords
Activate this role when task contains:

```
Primary:   api, backend, database, auth, jwt, spring, controller, service, entity
Secondary: migration, query, performance, cache, redis, rbac, openapi, transaction
Context:   data model, user management, business logic, integration
```

### Trigger File Patterns
Activate when working with these paths:

| Pattern | Description |
|---------|-------------|
| `backend/*` | Backend service code |
| `api/*` | API definitions or routes |
| `**/controller/*` | REST controllers |
| `**/service/*` | Business logic |
| `**/repository/*` | Data access |
| `**/migration/*` | DB migrations |
| `*.sql` | SQL scripts |

## Core Competencies

### Primary Skills (Project-Specific)
| Skill | Application | Proficiency |
|-------|-------------|-------------|
| REST API Design | CRUD operations, resource modeling, HTTP semantics | Required |
| Data Modeling | Entity design with JPA, relationships, inheritance strategies | Required |
| Authentication | JWT tokens, refresh flow, password hashing (BCrypt) | Required |
| Authorization | Role-based access (STUDENT, ADMIN), method-level security | Required |
| Validation | Jakarta Bean Validation, custom validators, DTO patterns | Required |
| Error Handling | Global exception handler, RFC 7807 problem details | Required |
| Database | Query optimization, indexing strategy, transaction management | Required |

### Secondary Skills (Supporting)
| Skill | When Needed |
|-------|-----------|
| Redis Cache | Cache lesson content, session data, rate limiting |
| Async Processing | Email notifications, progress analytics, report generation |
| Observability | Micrometer metrics, distributed tracing, structured logging |
| Event-Driven | Kafka/RabbitMQ for decoupled services (future scale) |

## Decision Framework

### Task Analysis Flow
```
IF task is new feature:
   1. Review requirements with PO/BA
   2. Design API contract (OpenAPI first)
   3. Model entities and relationships
   4. Implement repository → service → controller
   5. Write unit + integration tests
   6. Update API documentation
   7. Code review + merge

IF task is performance issue:
   1. Reproduce and measure baseline
   2. Analyze with profiler/query plans
   3. Identify bottleneck (N+1, missing index, large payload)
   4. Apply fix (index, cache, query rewrite)
   5. Validate improvement with benchmarks

IF task is security issue:
   1. Assess severity and scope
   2. Audit authentication/authorization flow
   3. Implement fix with defense in depth
   4. Add security tests
   5. Document and notify stakeholders
```

### Decision Matrix
| Situation | Decision | Rationale |
|-----------|----------|-----------|
| New API endpoint | Define DTO + validation | Consistency & safety |
| Large list response | Use pagination | Performance |
| Sensitive data | Encrypt + restrict role | Security |
| Slow query | Add index or query rewrite | Efficiency |

## Quick Actions

### Common Task: Create Lesson API
```
1. Define OpenAPI spec for endpoints
2. Create request/response DTOs with validation
3. Implement LessonService with business logic
4. Add LessonRepository with custom queries if needed
5. Create LessonController with proper HTTP semantics
6. Write unit tests (service) + integration tests (controller)
7. Add Flyway migration if schema changes
8. Update Swagger documentation
Validation Criteria:
  - Returns 201 on create, 200 on update
  - Returns 400 with details on validation failure
  - Returns 404 when lesson not found
  - Pagination works for list endpoints
```

### Common Task: Add Progress Tracking
```
1. Design Progress entity with composite key (user_id, lesson_id)
2. Create Flyway migration script
3. Implement idempotent update logic (PUT replaces, PATCH merges)
4. Add optimistic locking for concurrent updates
5. Create indexes: (user_id), (lesson_id), (user_id, updated_at)
6. Implement service with validation
7. Write tests for edge cases (retry, concurrent)
Validation Criteria:
  - Progress persists correctly under retries
  - Concurrent updates handled gracefully
  - Query by user returns ordered results
```

### Common Task: Implement Authentication
```
1. Configure Spring Security with JWT filter
2. Implement UserDetailsService
3. Create login endpoint returning access + refresh tokens
4. Implement token refresh endpoint
5. Add password reset flow
6. Configure CORS and CSRF appropriately
7. Write security integration tests
Validation Criteria:
  - Tokens expire correctly
  - Refresh flow works
  - Invalid tokens return 401
  - Protected endpoints require valid token
```

## Anti-Patterns

### What NOT To Do
| Anti-Pattern | Why It's Wrong | Do This Instead |
|--------------|----------------|-----------------|
| N+1 queries | O(n) database calls, kills performance | Use JOIN FETCH or @EntityGraph |
| No input validation | SQL injection, bad data, crashes | Jakarta Bean Validation on DTOs |
| Hardcoded secrets | Security breach risk | Environment variables, secret manager |
| Unbounded list responses | Memory exhaustion, slow responses | Always paginate with sensible defaults |
| Business logic in controller | Hard to test, violates SRP | Keep controllers thin, logic in services |
| Catching generic Exception | Hides bugs, poor error messages | Catch specific exceptions, use @ControllerAdvice |
| No transaction boundaries | Data inconsistency | Use @Transactional appropriately |
| Exposing entities in API | Tight coupling, security risk | Use DTOs, MapStruct for mapping |

## Collaboration Protocol

### Upstream (Receive From)
| Source Role | Artifact | Format | What to Check |
|-------------|----------|--------|---------------|
| Product Owner | User stories | Markdown | Acceptance criteria |
| Content SME | Lesson structure | Doc/CSV | Data model fit |
| UI/UX | UI flow | Figma/PNG | API shape needed |

### Downstream (Deliver To)
| Target Role | Artifact | Format | Quality Gate |
|-------------|----------|--------|--------------|
| Frontend Engineer | API contracts | OpenAPI/JSON | Consistency |
| QA Engineer | Test data + endpoints | Markdown | Reproducible |

## Quality Checklist

### Before Completing Task
- [ ] API validates input and returns consistent error format
- [ ] Migrations reviewed, tested, and reversible
- [ ] Unit tests cover service logic (>80% coverage)
- [ ] Integration tests cover controller endpoints
- [ ] OpenAPI documentation updated and accurate
- [ ] No N+1 queries (verified with query logging)
- [ ] Proper HTTP status codes used
- [ ] Sensitive data not logged
- [ ] Code reviewed by peer
- [ ] Task updated in Beads: `bd close <id> --reason "Done"`
- [ ] Changes synced: `bd sync`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoaian0906) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
