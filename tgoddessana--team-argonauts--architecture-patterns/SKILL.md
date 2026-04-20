---
name: architecture-patterns
description: | Use when this capability is needed.
metadata:
  author: tgoddessana
---

# Architecture Patterns Catalog

Battle-tested architectural patterns from Team Argonauts' FAANG experience. Every pattern includes trade-offs, when to use, and when to avoid.

## Core Principle

> "Architecture is the decisions that are hard to change later. Choose wisely."

**Remember:**
- Patterns solve specific problems
- Every pattern has costs
- Start simple, evolve as needed
- Your team's ability to operate matters

---

## Application Architecture

### Monolith

**What:** Single deployable unit containing all functionality.

**When to use:**
- Small team (< 10 engineers)
- MVP or early-stage product
- Shared data model across features
- Fast iteration is critical

**When to avoid:**
- Multiple teams need independent deployment
- Different parts have different scaling needs
- Technology diversity is required

**Trade-offs:**
```
✅ Simple deployment, easy debugging, strong consistency
❌ Scales all-or-nothing, longer deploy times, coupling risk
```

**Example:**
```
Startup with 5 engineers building SaaS product
→ Use monolith for 2-3 years minimum
→ Split when team grows to 20+
```

---

### Microservices

**What:** Multiple independently deployable services, each owning its data.

**When to use:**
- Multiple teams (20+ engineers)
- Independent scaling needs
- Different technology needs per service
- Organizational independence required

**When to avoid:**
- Small team (< 10 engineers)
- Immature product/domain
- Strong consistency required across features
- Limited operational capacity

**Trade-offs:**
```
✅ Independent deployment, technology freedom, clear boundaries
❌ Distributed complexity, eventual consistency, operational overhead
```

**Anti-pattern:**
```
3-person startup adopting microservices
→ Operational burden > benefits
→ Use monolith until organizational need exists
```

---

### Modular Monolith

**What:** Monolith with strong internal boundaries, modules could become services.

**When to use:**
- Medium team (10-20 engineers)
- Want benefits of both approaches
- Future microservices possible
- Domain boundaries are clear

**Trade-offs:**
```
✅ Monolith simplicity + microservice-ready, easier to evolve
❌ Requires discipline, boundaries can blur
```

**Pattern:**
```
src/
├── users/         # Bounded context
│   ├── api/
│   ├── domain/
│   └── data/
├── orders/        # Bounded context
└── payments/      # Bounded context

Rules:
- No direct imports between contexts
- Communication via defined APIs
- Each module owns its data
```

---

## Data Architecture

### Database per Service

**What:** Each service has its own database, no shared DB access.

**When to use:**
- Microservices architecture
- Independent scaling of data
- Different data technology needs
- Team autonomy required

**When to avoid:**
- Need strong consistency across services
- Complex cross-service queries
- Small scale (< 100K users)

**Trade-offs:**
```
✅ Service independence, schema freedom, clear ownership
❌ No joins across services, eventual consistency, data duplication
```

---

### Shared Database

**What:** Multiple services/modules sharing one database.

**When to use:**
- Monolith or modular monolith
- Strong consistency required
- Complex relational queries needed
- Small/medium scale

**When to avoid:**
- Multiple teams need independence
- Different scaling needs per service

**Trade-offs:**
```
✅ Easy queries, strong consistency, simple transactions
❌ Coupling, coordination needed for schema changes
```

---

### Event Sourcing

**What:** Store events, not current state. Derive state by replaying events.

**When to use:**
- Audit trail is critical
- Time travel/replay needed
- Complex business logic
- Event-driven architecture

**When to avoid:**
- Simple CRUD application
- Team unfamiliar with pattern
- Query complexity not worth it

**Trade-offs:**
```
✅ Complete audit trail, time travel, event-driven
❌ Query complexity, storage growth, learning curve
```

---

## Communication Patterns

### Synchronous (REST/gRPC)

**What:** Request-response, caller waits for result.

**When to use:**
- Read operations
- Immediate response needed
- Simple request-response flow

**When to avoid:**
- Long-running operations
- Caller doesn't need immediate response
- High decoupling needed

**Trade-offs:**
```
✅ Simple mental model, immediate feedback, easy debugging
❌ Coupling, cascading failures, blocking
```

**Best practices:**
```javascript
// Always set timeouts
const response = await fetch(url, {
  signal: AbortSignal.timeout(5000)
})

// Circuit breaker for resilience
if (circuitBreaker.isOpen()) {
  return fallbackValue
}
```

---

### Asynchronous (Message Queue)

**What:** Producer sends message, doesn't wait. Consumer processes later.

**When to use:**
- Long-running operations
- Decoupling needed
- Load leveling required
- At-least-once delivery acceptable

**When to avoid:**
- Immediate response needed
- Exactly-once required (use carefully)
- Added complexity not worth it

**Trade-offs:**
```
✅ Decoupling, resilience, load leveling, scalability
❌ Eventual consistency, complexity, harder debugging
```

**Example:**
```javascript
// Producer
await queue.publish('order.created', { orderId: 123 })
// Don't wait for processing

// Consumer
queue.subscribe('order.created', async (message) => {
  // Process asynchronously
  await processOrder(message.orderId)
})
```

---

### Event-Driven Architecture

**What:** Services emit events when state changes, others react.

**When to use:**
- Multiple services need to react to changes
- Loose coupling desired
- Audit trail needed
- Complex workflows

**When to avoid:**
- Simple request-response sufficient
- Team unfamiliar with pattern
- Debugging async flows is too hard

**Trade-offs:**
```
✅ Loose coupling, extensibility, scalability
❌ Harder debugging, eventual consistency, complexity
```

---

## Scalability Patterns

### Caching

**Levels:**
```
Client → CDN → App Cache → Database Cache → Database
```

**When to cache:**
- Read >> Write (10:1 or higher)
- Data changes infrequently
- Expensive computation
- Database load high

**Cache strategies:**

**Cache-Aside:**
```javascript
let data = cache.get(key)
if (!data) {
  data = db.query(key)
  cache.set(key, data, ttl)
}
return data
```

**Write-Through:**
```javascript
db.write(key, value)
cache.set(key, value)  // Sync
```

**Cache invalidation:**
```
1. TTL (time-based, simple, stale data possible)
2. Event-based (accurate, more complex)
3. Manual (full control, coordination needed)
```

---

### Horizontal Scaling

**What:** Add more instances to handle load.

**When to use:**
- Stateless services
- Load is distributable
- Need elasticity

**Requirements:**
- Load balancer
- Stateless services (or external state)
- Shared data store

**Trade-offs:**
```
✅ Linear scalability, fault tolerance, elasticity
❌ Complexity, coordination overhead
```

---

### Vertical Scaling

**What:** Add more resources (CPU, RAM) to existing instance.

**When to use:**
- Quick fix for capacity
- Stateful services
- Horizontal scaling too complex

**Trade-offs:**
```
✅ Simple, no code changes, good for DB
❌ Limits, downtime, single point of failure
```

---

## Resilience Patterns

### Circuit Breaker

**What:** Stop calling failing service, fail fast, retry periodically.

**States:**
```
Closed (normal) → Open (failing) → Half-Open (testing) → Closed
```

**When to use:**
- Calling external services
- Cascading failures possible
- Graceful degradation needed

**Example:**
```javascript
if (circuitBreaker.isOpen()) {
  return fallbackValue
}

try {
  const result = await externalService.call()
  circuitBreaker.recordSuccess()
  return result
} catch (error) {
  circuitBreaker.recordFailure()
  throw error
}
```

---

### Retry with Backoff

**What:** Retry failed operations with increasing delays.

**When to use:**
- Transient failures expected
- Idempotent operations
- Network calls

**Pattern:**
```javascript
const maxRetries = 3
const baseDelay = 100

for (let i = 0; i < maxRetries; i++) {
  try {
    return await operation()
  } catch (error) {
    if (i === maxRetries - 1) throw error

    // Exponential backoff with jitter
    const delay = baseDelay * Math.pow(2, i) * (0.5 + Math.random())
    await sleep(delay)
  }
}
```

---

### Bulkhead

**What:** Isolate resources so failure in one doesn't affect others.

**Example:**
```javascript
// Separate connection pools
const criticalPool = createPool({ size: 20 })
const batchPool = createPool({ size: 5 })

// Critical operations use critical pool
// Batch jobs use batch pool
// Batch job failures don't exhaust critical pool
```

---

## Choosing Patterns

### Decision Framework

**1. Understand the problem**
- What are we actually solving?
- What's the scale?
- What's the team capacity?

**2. Start simple**
- Monolith before microservices
- Synchronous before async
- Vertical before horizontal

**3. Evolve as needed**
- Measure before optimizing
- One pattern at a time
- Document decisions

**4. Consider trade-offs**
- What does this cost?
- Can we operate it?
- Is it reversible?

---

## Common Mistakes

### Premature Microservices
```
❌ 3-person startup using microservices
✅ Start with monolith, split when team grows
```

### Over-caching
```
❌ Cache everything with long TTL
✅ Cache hot paths with appropriate TTL
```

### Event-Driven Everything
```
❌ All operations async via events
✅ Use events where decoupling adds value
```

### No Architecture
```
❌ "We'll figure it out as we go"
✅ Document key decisions, review regularly
```

---

## References

For detailed implementation guides, see files in `references/` directory.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tgoddessana) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
