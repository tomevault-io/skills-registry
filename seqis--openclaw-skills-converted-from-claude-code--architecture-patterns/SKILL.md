---
name: architecture-patterns
description: Software architecture patterns and design principles for scalable systems Use when this capability is needed.
metadata:
  author: seqis
---

# Architecture Patterns Skill

## Overview

Software architecture decision frameworks and patterns. Provides guidance on when to use which patterns, API design, data layer patterns, and common anti-patterns to avoid.

## Type

reference

## When to Invoke

**Trigger keywords:** architecture, design pattern, API design, microservices, data layer, CQRS, repository pattern, clean architecture, DDD

**Trigger phrases:**
- "how should I structure..."
- "what pattern should I use"
- "microservices vs monolith"
- "API design for..."
- "data access pattern"

## Architecture Decision Framework

When making architectural decisions, ask:

### 1. Scale Questions
- Current user count? Expected growth?
- Request volume? Peaks?
- Data volume? Growth rate?
- Team size? Expected growth?

### 2. Complexity Questions
- How many bounded contexts/domains?
- Integration requirements?
- Real-time requirements?
- Consistency requirements (strong vs eventual)?

### 3. Constraint Questions
- Budget constraints?
- Timeline pressure?
- Team expertise?
- Regulatory/compliance needs?

## Application Architecture Patterns

### Monolith
**Use when:**
- Starting new project
- Small team (1-5 developers)
- Single domain
- Need to move fast

**Avoid when:**
- Multiple teams
- Independent scaling needed
- Different deployment schedules

```
┌─────────────────────────────────────┐
│           MONOLITH                  │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐   │
│  │ UI  │ │Users│ │Order│ │ Pay │   │
│  └──┬──┘ └──┬──┘ └──┬──┘ └──┬──┘   │
│     └───────┴───────┴───────┘       │
│              Database               │
└─────────────────────────────────────┘
```

### Modular Monolith
**Use when:**
- Growing monolith
- Preparing for microservices
- Need better boundaries
- Team growing

```
┌─────────────────────────────────────┐
│        MODULAR MONOLITH             │
│  ┌─────────┐  ┌─────────┐           │
│  │ Users   │  │ Orders  │           │
│  │ Module  │  │ Module  │  ...      │
│  └────┬────┘  └────┬────┘           │
│       │            │                │
│  ┌────┴────┐  ┌────┴────┐           │
│  │Users DB │  │Orders DB│           │
│  └─────────┘  └─────────┘           │
└─────────────────────────────────────┘
```

### Microservices
**Use when:**
- Large team (10+ developers)
- Independent deployability critical
- Different scaling requirements
- Clear domain boundaries

**Avoid when:**
- Small team
- Unclear boundaries
- Premature optimization

```
┌─────────┐    ┌─────────┐    ┌─────────┐
│ Users   │    │ Orders  │    │ Payment │
│ Service │◄──►│ Service │◄──►│ Service │
└────┬────┘    └────┬────┘    └────┬────┘
     │              │              │
┌────┴────┐   ┌────┴────┐    ┌────┴────┐
│Users DB │   │Orders DB│    │Payments │
└─────────┘   └─────────┘    │   DB    │
                             └─────────┘
```

## API Design Patterns

### REST
**Use when:**
- Resource-based operations (CRUD)
- HTTP semantics fit well
- Simple client needs
- Caching important

```
GET    /users           # List users
GET    /users/{id}      # Get user
POST   /users           # Create user
PUT    /users/{id}      # Update user
DELETE /users/{id}      # Delete user
```

### GraphQL
**Use when:**
- Complex, nested data requirements
- Multiple client types (web, mobile)
- Over-fetching is a problem
- Rapid frontend iteration

```graphql
query GetUser($id: ID!) {
  user(id: $id) {
    name
    orders {
      id
      total
      items { name }
    }
  }
}
```

### gRPC
**Use when:**
- Service-to-service communication
- Performance critical
- Strong typing required
- Streaming needed

```protobuf
service UserService {
  rpc GetUser(GetUserRequest) returns (User);
  rpc StreamUpdates(Empty) returns (stream UserUpdate);
}
```

### Decision Matrix

| Factor | REST | GraphQL | gRPC |
|--------|------|---------|------|
| Learning curve | Low | Medium | High |
| Tooling | Excellent | Good | Growing |
| Performance | Good | Good | Excellent |
| Browser support | Native | Native | Limited |
| Caching | Excellent | Complex | Limited |

## Data Access Patterns

### Repository Pattern
**Use when:**
- Need to abstract data access
- Unit testing with mocks
- Potential DB changes

```python
class UserRepository:
    def get(self, id: str) -> User:
        ...
    def save(self, user: User) -> None:
        ...
    def delete(self, id: str) -> None:
        ...
```

### CQRS (Command Query Responsibility Segregation)
**Use when:**
- Read and write loads differ
- Complex read models
- Event sourcing

```
Commands (Writes)          Queries (Reads)
     │                          │
     ▼                          ▼
┌─────────┐              ┌─────────┐
│ Command │              │  Query  │
│ Handler │              │ Handler │
└────┬────┘              └────┬────┘
     │                        │
     ▼                        ▼
┌─────────┐              ┌─────────┐
│ Write   │   ──sync──►  │  Read   │
│   DB    │              │   DB    │
└─────────┘              └─────────┘
```

### Event Sourcing
**Use when:**
- Complete audit trail required
- Time-travel debugging valuable
- Complex domain logic

```
Events: [
  UserCreated(id=1, name="Alice"),
  UserEmailChanged(id=1, email="alice@new.com"),
  UserDeactivated(id=1)
]
Current State: { id: 1, name: "Alice", email: "alice@new.com", active: false }
```

## Layer Architecture

### Clean Architecture / Hexagonal

```
┌──────────────────────────────────────────┐
│              Presentation                 │
│         (Controllers, Views)              │
├──────────────────────────────────────────┤
│              Application                  │
│          (Use Cases, DTOs)               │
├──────────────────────────────────────────┤
│               Domain                      │
│      (Entities, Business Rules)          │
├──────────────────────────────────────────┤
│            Infrastructure                 │
│    (Database, External Services)         │
└──────────────────────────────────────────┘
```

**Dependency Rule:** Dependencies point inward. Domain has no external dependencies.

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Big Ball of Mud | No structure, tangled code | Modular design, clear boundaries |
| Distributed Monolith | Microservices with coupling | True boundaries, event-driven |
| God Object | One class does everything | Single responsibility, decompose |
| Anemic Domain | Logic in services, not entities | Rich domain models |
| Leaky Abstraction | Implementation details exposed | Proper encapsulation |
| Premature Optimization | Over-engineering for scale | Start simple, measure, optimize |
| Cargo Cult Architecture | Copying without understanding | Understand patterns first |

## Pattern Selection Algorithm

```
Is domain complex?
├─ No → Monolith with simple layers
└─ Yes →
    Is team large (10+)?
    ├─ No → Modular Monolith
    └─ Yes →
        Are boundaries clear?
        ├─ No → Define domains first (DDD)
        └─ Yes → Microservices
```

## Integration

Works with:
- `project-scaffolding` - Structure based on chosen pattern
- `documentation-standards` - Document architectural decisions
- `/newapp` command - Invokes during planning phase

---

*Reference patterns from DDD, Clean Architecture, and microservices literature*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
