---
name: architecture
description: Design, evaluate, and document software architectures including system design, design patterns, architecture patterns, scalability planning, and technology selection. Use when designing systems, choosing architectures, evaluating design decisions, or when user mentions architecture, system design, or scalability. Use when this capability is needed.
metadata:
  author: josavicentevw
---

# Architecture

A comprehensive architecture skill that helps design, evaluate, and document software architectures for robust, scalable, and maintainable systems.

## Quick Start

Basic architecture workflow:

```
# Understand requirements (functional + non-functional)
# Identify constraints and trade-offs
# Design system components and relationships
# Document architecture decisions
# Validate against requirements
```

## Core Capabilities

### 1. System Architecture Design

Design complete system architectures:

- **Monolithic**: Single deployable unit
- **Microservices**: Distributed services architecture
- **Serverless**: Event-driven, function-based
- **Event-Driven**: Asynchronous message-based
- **Layered**: Separation of concerns in layers
- **Hexagonal**: Ports and adapters pattern
- **CQRS**: Command Query Responsibility Segregation
- **Event Sourcing**: State as sequence of events

### 2. Design Patterns

Apply proven design patterns:

**Creational Patterns:**
- Singleton, Factory, Builder, Prototype, Abstract Factory

**Structural Patterns:**
- Adapter, Bridge, Composite, Decorator, Facade, Proxy

**Behavioral Patterns:**
- Observer, Strategy, Command, State, Template Method, Chain of Responsibility

### 3. Architecture Quality Attributes

Evaluate and optimize for:

- **Performance**: Response time, throughput, resource usage
- **Scalability**: Horizontal and vertical scaling
- **Availability**: Uptime, fault tolerance, disaster recovery
- **Security**: Authentication, authorization, encryption, data protection
- **Maintainability**: Code quality, modularity, testability
- **Reliability**: Error handling, resilience, redundancy
- **Usability**: User experience, API design
- **Observability**: Logging, monitoring, tracing

### 4. Technology Selection

Evaluate and recommend technologies:

- **Databases**: SQL vs NoSQL, selection criteria
- **Message Queues**: Kafka, RabbitMQ, SQS
- **Caching**: Redis, Memcached, CDN
- **API Protocols**: REST, GraphQL, gRPC
- **Cloud Platforms**: AWS, Azure, GCP
- **Containerization**: Docker, Kubernetes

### 5. Architecture Documentation

Document architecture effectively:

- **C4 Model**: Context, Container, Component, Code diagrams
- **Architecture Decision Records (ADRs)**: Document key decisions
- **Data Flow Diagrams**: How data moves through system
- **Sequence Diagrams**: Component interactions
- **Deployment Diagrams**: Infrastructure and deployment

## Architecture Patterns

### Microservices Architecture

```
┌─────────────────────────────────────────────────┐
│              API Gateway / BFF                   │
└────────┬──────────┬──────────┬──────────────────┘
         │          │          │
    ┌────▼───┐ ┌───▼────┐ ┌──▼──────┐
    │ User   │ │ Order  │ │ Payment │
    │Service │ │Service │ │ Service │
    └────┬───┘ └───┬────┘ └──┬──────┘
         │         │          │
    ┌────▼───┐ ┌──▼─────┐ ┌──▼──────┐
    │ User   │ │ Order  │ │ Payment │
    │  DB    │ │   DB   │ │   DB    │
    └────────┘ └────────┘ └─────────┘
         │         │          │
    └────┴─────────┴──────────┴──────┘
              Message Bus
```

**Characteristics:**
- Independent deployment and scaling
- Polyglot persistence
- Decentralized data management
- Resilience through isolation
- Technology diversity

**Trade-offs:**
- ✅ Independent scaling
- ✅ Technology flexibility
- ✅ Fault isolation
- ❌ Distributed system complexity
- ❌ Data consistency challenges
- ❌ Operational overhead

### Event-Driven Architecture

```
┌──────────┐      ┌──────────────┐      ┌──────────┐
│ Producer │─────▶│ Event Bus    │─────▶│Consumer 1│
└──────────┘      │ (Kafka/SNS)  │      └──────────┘
                  └───────┬──────┘
                          │
                     ┌────▼──────┐
                     │Consumer 2 │
                     └───────────┘
```

**Characteristics:**
- Asynchronous communication
- Loose coupling between components
- Scalable event processing
- Event replay capability

**Use Cases:**
- Real-time data processing
- Microservices integration
- IoT systems
- Activity tracking

### Layered Architecture

```
┌─────────────────────────────────┐
│     Presentation Layer          │ ← Controllers, Views
├─────────────────────────────────┤
│     Business Logic Layer        │ ← Services, Domain
├─────────────────────────────────┤
│     Data Access Layer           │ ← Repositories, DAOs
├─────────────────────────────────┤
│     Database Layer              │ ← Database
└─────────────────────────────────┘
```

**Characteristics:**
- Clear separation of concerns
- Each layer has specific responsibility
- Dependencies flow downward
- Easy to understand and maintain

### Hexagonal Architecture (Ports & Adapters)

```
         ┌─────────────────────────┐
         │   External Systems      │
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │      Adapters           │ ← HTTP, CLI, Message Queue
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │       Ports             │ ← Interfaces
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │    Domain Logic         │ ← Core Business Logic
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │       Ports             │ ← Interfaces
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │      Adapters           │ ← Database, APIs, File System
         └───────────┬─────────────┘
                     │
         ┌───────────▼─────────────┐
         │   External Systems      │
         └─────────────────────────┘
```

**Characteristics:**
- Domain logic independent of external concerns
- Testable in isolation
- Flexible adapter implementation
- Clear boundaries

## Scalability Patterns

### Horizontal Scaling

```
           ┌─────────────┐
           │Load Balancer│
           └──────┬──────┘
         ┌────────┼────────┐
    ┌────▼───┐ ┌─▼────┐ ┌─▼─────┐
    │Server 1│ │Server│ │Server │
    │        │ │  2   │ │   3   │
    └────┬───┘ └─┬────┘ └─┬─────┘
         └───────┴────────┘
                 │
          ┌──────▼──────┐
          │   Database  │
          └─────────────┘
```

**Techniques:**
- Load balancing
- Stateless services
- Shared data layer
- Session management

### Caching Strategy

```
Client ──▶ CDN ──▶ API Server ──▶ Redis ──▶ Database
           (Static)  (Cache)     (Cache)    (Source)
```

**Cache Levels:**
1. CDN: Static assets
2. Application Cache: Query results, computed data
3. Database Cache: Query cache

**Cache Patterns:**
- Cache-Aside: Application manages cache
- Read-Through: Cache loads data automatically
- Write-Through: Write to cache and DB
- Write-Behind: Async writes to DB

### Database Scaling

**Vertical Scaling:**
- Increase server resources
- Limited by hardware

**Horizontal Scaling:**
- **Replication**: Master-Slave, Multi-Master
- **Sharding**: Partition data across servers
- **CQRS**: Separate read and write databases

## Architecture Decision Framework

### Decision Template

```markdown
# Decision: [Title]

## Context
- What problem are we solving?
- What are the constraints?
- What are the requirements?

## Options Considered

### Option 1: [Name]
**Pros:**
- Pro 1
- Pro 2

**Cons:**
- Con 1
- Con 2

**Estimated Effort:** [Low/Medium/High]
**Risk Level:** [Low/Medium/High]

### Option 2: [Name]
[Same structure]

## Decision
We chose [Option X] because [reasoning].

## Consequences
- Positive: [benefits]
- Negative: [trade-offs]
- Risks: [what could go wrong]
- Mitigation: [how to address risks]

## Validation
How will we validate this decision?
```

## Common Architecture Patterns

### API Gateway Pattern

```python
"""
API Gateway centralizes external requests and routes to services.
"""

class APIGateway:
    def __init__(self):
        self.user_service = UserService()
        self.order_service = OrderService()
        self.auth_service = AuthService()
    
    async def handle_request(self, request: Request) -> Response:
        # Authentication
        if not await self.auth_service.authenticate(request):
            return Response(status=401)
        
        # Rate limiting
        if not await self.rate_limiter.check(request.user_id):
            return Response(status=429)
        
        # Route to appropriate service
        if request.path.startswith('/users'):
            return await self.user_service.handle(request)
        elif request.path.startswith('/orders'):
            return await self.order_service.handle(request)
        
        return Response(status=404)
```

### Circuit Breaker Pattern

```python
"""
Circuit breaker prevents cascading failures.
"""

class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.failures = 0
        self.last_failure_time = None
        self.state = 'CLOSED'  # CLOSED, OPEN, HALF_OPEN
    
    async def call(self, func, *args, **kwargs):
        if self.state == 'OPEN':
            if self._should_attempt_reset():
                self.state = 'HALF_OPEN'
            else:
                raise CircuitBreakerOpen('Service unavailable')
        
        try:
            result = await func(*args, **kwargs)
            self._on_success()
            return result
        except Exception as e:
            self._on_failure()
            raise
    
    def _on_success(self):
        self.failures = 0
        self.state = 'CLOSED'
    
    def _on_failure(self):
        self.failures += 1
        self.last_failure_time = time.time()
        if self.failures >= self.failure_threshold:
            self.state = 'OPEN'
    
    def _should_attempt_reset(self):
        return (time.time() - self.last_failure_time) >= self.timeout
```

### Repository Pattern

```python
"""
Repository pattern abstracts data access.
"""

from abc import ABC, abstractmethod
from typing import List, Optional

class UserRepository(ABC):
    @abstractmethod
    async def find_by_id(self, user_id: int) -> Optional[User]:
        pass
    
    @abstractmethod
    async def find_by_email(self, email: str) -> Optional[User]:
        pass
    
    @abstractmethod
    async def save(self, user: User) -> User:
        pass
    
    @abstractmethod
    async def delete(self, user_id: int) -> bool:
        pass

class SQLUserRepository(UserRepository):
    def __init__(self, db_session):
        self.db = db_session
    
    async def find_by_id(self, user_id: int) -> Optional[User]:
        result = await self.db.execute(
            "SELECT * FROM users WHERE id = ?", (user_id,)
        )
        row = result.fetchone()
        return User.from_row(row) if row else None
    
    async def save(self, user: User) -> User:
        if user.id:
            await self.db.execute(
                "UPDATE users SET name = ?, email = ? WHERE id = ?",
                (user.name, user.email, user.id)
            )
        else:
            result = await self.db.execute(
                "INSERT INTO users (name, email) VALUES (?, ?)",
                (user.name, user.email)
            )
            user.id = result.lastrowid
        return user
```

## System Design Process

### 1. Requirements Gathering

**Functional Requirements:**
- What should the system do?
- What features are needed?
- What are the use cases?

**Non-Functional Requirements:**
- Performance: Latency, throughput targets
- Scale: Expected users, data volume
- Availability: Uptime requirements
- Security: Compliance, data protection
- Cost: Budget constraints

### 2. Capacity Planning

```
Users: 10 million
Daily Active Users: 1 million
Requests per second: 1M users × 10 requests/day / 86400 seconds ≈ 116 RPS
Peak traffic (3x average): 350 RPS

Data:
- Per user: 1 KB metadata + 100 KB content
- Total: 10M × 101 KB ≈ 1 TB

Bandwidth:
- Request size: 1 KB
- Response size: 10 KB
- Bandwidth: 350 RPS × 11 KB ≈ 3.85 MB/s ≈ 30 Mbps
```

### 3. High-Level Design

```
┌─────────┐
│ Client  │
└────┬────┘
     │
┌────▼──────────┐
│  CDN          │ (Static content)
└────┬──────────┘
     │
┌────▼──────────┐
│ Load Balancer │
└────┬──────────┘
     │
┌────▼──────────┐
│  Web Servers  │ (3+ instances)
└────┬──────────┘
     │
┌────▼──────────┐
│  App Servers  │ (5+ instances)
└────┬──────────┘
     │
┌────▼──────────┬────────────┐
│               │            │
│  Cache       │  Database  │ Message
│  (Redis)     │  (Master/  │ Queue
│              │   Slaves)  │ (Kafka)
└──────────────┴────────────┴─────────┘
```

### 4. Detailed Component Design

Design each component with:
- Inputs and outputs
- Data models
- API contracts
- Error handling
- Monitoring

### 5. Identify Bottlenecks

- Single points of failure
- Performance bottlenecks
- Scaling limitations
- Data consistency issues

### 6. Optimization

- Caching strategy
- Database indexing
- Load balancing
- CDN for static content
- Async processing
- Connection pooling

## Best Practices

1. **Start Simple**: Begin with simplest architecture that works
2. **Design for Failure**: Assume components will fail
3. **Loose Coupling**: Minimize dependencies between components
4. **High Cohesion**: Group related functionality
5. **Separation of Concerns**: Each component has single responsibility
6. **Document Decisions**: Use ADRs for important choices
7. **Consider Trade-offs**: Every decision has pros and cons
8. **Plan for Scale**: Design with growth in mind
9. **Security First**: Build security in from the start
10. **Measure Everything**: Observability is crucial

## When to Use This Skill

Use this skill when:
- Designing new systems
- Evaluating architecture options
- Planning system migrations
- Addressing scalability issues
- Making technology decisions
- Documenting architecture
- Conducting architecture reviews
- Planning for growth
- Solving system design problems
- Training team on architecture patterns

## Examples

See [EXAMPLES.md](EXAMPLES.md) for complete architecture examples including:
- E-commerce system design
- Social media platform
- Video streaming service
- Real-time analytics system
- Multi-tenant SaaS application

For architecture templates, see [templates/](templates/).

For architecture decision records, see [adr/](adr/).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/josavicentevw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
