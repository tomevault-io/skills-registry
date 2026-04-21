---
name: architectural-pattern-discovery
description: Discovers architectural and design patterns across all abstraction levels. Analyzes structural patterns, component relationships, recurring solution approaches, and design principles. Works with any technology stack without prior framework knowledge to provide comprehensive pattern understanding from code-level to system-level architecture. Use when this capability is needed.
metadata:
  author: xilnick
---

## Pattern Description

**What**: Systematically analyzes a codebase to identify recurring architectural, design, and coding patterns across all abstraction levels, from low-level implementation idioms to high-level system architecture, including project-specific solution approaches and technology integration patterns.

**When**: Use this skill to understand the complete pattern landscape of a project, during architectural assessments, for refactoring efforts, when onboarding to complex systems, or when planning technology evolution and modernization strategies.

**Context**: Applicable across all technology stacks and project types, focusing on observable patterns rather than theoretical frameworks, with emphasis on understanding how patterns actually manifest in practice.

## Project-Specific Conventions

### Architectural Patterns
- **Overall Structure**: [e.g., Monolithic, Microservices, Layered, Event-Driven, Serverless]
- **Data Management**: [e.g., Centralized Database, Distributed Databases, CQRS, Event Sourcing]
- **Communication**: [e.g., RESTful APIs, gRPC, Message Queues, WebSockets, GraphQL]
- **Deployment**: [e.g., Container-based, Serverless, Traditional hosting, Multi-cloud]

### Design Patterns
- **Structural**: [e.g., Adapter, Decorator, Facade, Proxy, Bridge, Composite]
- **Behavioral**: [e.g., Observer, Strategy, Command, State, Iterator, Mediator]
- **Creational**: [e.g., Factory, Singleton, Builder, Prototype, Abstract Factory]

### Project-Specific Solution Patterns
- **Feature Templates**: [e.g., how new features are consistently structured and integrated]
- **Data Processing**: [e.g., standard data transformation and validation pipelines]
- **Error Handling**: [e.g., consistent error response patterns and recovery strategies]
- **Configuration**: [e.g., how configuration is managed and propagated across systems]

### Technology Integration Patterns
- **Framework Integration**: [e.g., how external libraries and frameworks are consistently integrated]
- **Database Patterns**: [e.g., ORM usage patterns, query optimization approaches, transaction handling]
- **API Design**: [e.g., consistent API structures, versioning strategies, response formats]
- **Security Patterns**: [e.g., authentication flows, authorization checks, data protection approaches]

## Common Pitfalls

### ❌ Implicit Patterns
**Problem**: Important architectural and design patterns exist but are not explicitly documented or understood by the team.
**Why It Fails**: Leads to inconsistent implementations, makes onboarding difficult, and creates technical debt through pattern drift.
**Better Approach**: Document discovered patterns using architectural decision records (ADRs) and maintain a living pattern catalog.

### ❌ Pattern Inconsistency
**Problem**: Similar problems are solved using different patterns without clear rationale, leading to architectural confusion.
**Why It Fails**: Increases cognitive load for developers, makes maintenance harder, and reduces system predictability.
**Better Approach**: Establish clear pattern selection guidelines and conduct regular architectural reviews to ensure consistency.

### ❌ Over-Engineering Patterns
**Problem**: Applying complex architectural patterns where simpler solutions would suffice.
**Why It Fails**: Increases unnecessary complexity, development time, and maintenance overhead without proportional benefits.
**Better Approach**: Choose patterns that match the problem's complexity; prioritize simplicity and evolve patterns as complexity grows.

## Related Resources

### Related Skills
- **convention-extraction**: For lower-level coding style and naming conventions
- **universal-technology-discovery**: To identify the technologies upon which patterns are built
- **integration-mapping-discovery**: For detailed analysis of component connections and data flow
- **operational-intelligence-discovery**: For understanding deployment and operational patterns

### Related Agents
- **architecture-specialist**: For in-depth analysis and recommendation of architectural patterns
- **lead-architect-agent**: For defining and evolving project-specific patterns and standards

### External Resources
- [Refactoring Guru - Design Patterns](https://refactoring.guru/design-patterns)
- [Martin Fowler - Architectural Patterns](https://martinfowler.com/architecture/)
- [Architectural Decision Records (ADR)](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
- [Domain-Driven Design (DDD)](https://martinfowler.com/bliki/DomainDrivenDesign.html)
- [Patterns of Enterprise Application Architecture](https://martinfowler.com/books/eaa.html)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xilnick) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
