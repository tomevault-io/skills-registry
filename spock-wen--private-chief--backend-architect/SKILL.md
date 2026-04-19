---
name: backend-architect
description: Expert Backend Architect for designing robust, scalable server-side systems, APIs, and database architectures. Use when designing backend systems, REST/GraphQL APIs, database schemas, implementing server logic, system scalability, security architecture, or when the user asks for backend architecture advice. Use when this capability is needed.
metadata:
  author: spock-wen
---

# Backend Architect

Expert guidance for designing performant, secure, and maintainable backend systems.

## When to Apply

Invoke this skill when the user needs:
- API design (REST, GraphQL) or versioning
- Database schema design or optimization
- Server-side logic architecture
- Scalability, high availability, or failover design
- Security implementation (auth, validation, compliance)
- Deployment, CI/CD, or observability setup
- Integration with third-party services or message queues

---

## API Design Checklist

### RESTful API Standards

| Check | Requirement |
|-------|-------------|
| HTTP methods | GET (read), POST (create), PUT/PATCH (update), DELETE |
| Status codes | 200, 201, 204, 400, 401, 403, 404, 422, 429, 500 |
| Resource naming | Plural nouns (`/users`, `/orders`), no verbs |
| Versioning | URL path (`/v1/`) or header; avoid breaking changes |
| Pagination | Cursor or offset; include total count when cheap |
| Rate limiting | Per-IP and per-user; return `Retry-After` header |

### GraphQL Considerations

- Use batching and DataLoader to prevent N+1
- Implement query complexity limits
- Document schema; prefer explicit over generic resolvers

### API Documentation

Include: request/response formats, auth requirements, error payloads, rate limits, examples.

---

## Database Architecture Decision Framework

### Schema Design

1. **Normalize** for data integrity; denormalize only for read hotspots
2. **Index strategy**: Cover frequent queries; avoid over-indexing writes
3. **Primary keys**: Prefer UUIDs for distributed systems; auto-increment for single-DB
4. **Soft delete**: Use `deleted_at` when audit trail matters

### Technology Choice

| Pattern | Prefer | Avoid |
|---------|--------|-------|
| Relational + ACID | PostgreSQL, MySQL | For graph/tree-heavy data without proper modeling |
| Document store | MongoDB | When joins/transactions are core |
| Caching | Redis, Memcached | As primary store without persistence strategy |
| Message queue | RabbitMQ, Kafka, SQS | Sync calls where async fits better |

### Migration Strategy

- Version-controlled migrations; never edit applied migrations
- Backward-compatible schema changes (add columns nullable first)
- Test rollback procedures

---

## Server-Side Architecture Patterns

### Layered Design

```
Controller/Resolver → Service (business logic) → Repository (data access)
```

- Stateless services for horizontal scaling
- Failover and circuit breakers for external calls
- Retry with exponential backoff; idempotency where possible

### Caching Layers

| Layer | Use Case | TTL consideration |
|-------|----------|-------------------|
| Application | Session, computed values | Short; invalidate on write |
| Database | Query result cache | Moderate; align with consistency needs |
| CDN | Static assets, public APIs | Long; versioned URLs |

### Background Jobs

- Use job queues (Bull, Celery, etc.) for non-blocking work
- Idempotent job handlers
- Dead-letter handling and alerting

---

## Security Checklist

- [ ] Auth: OAuth 2.0, JWT, or SAML; short-lived tokens; refresh token rotation
- [ ] Input validation: Whitelist; sanitize; parameterized queries (no raw SQL concatenation)
- [ ] XSS/CSRF: Proper headers; CORS; SameSite cookies
- [ ] Encryption: TLS in transit; encrypt PII at rest
- [ ] Secrets: Never in code; use env vars or secrets manager
- [ ] Compliance: GDPR (right to delete, data portability), CCPA, HIPAA as applicable

---

## Scalability & Performance

### Horizontal Scaling

- Stateless services; shared-nothing where possible
- Session in Redis/DB, not local memory
- Load balancer + health checks

### Database Performance

- Connection pooling
- Read replicas for read-heavy workloads
- Query profiling; avoid N+1
- Appropriate indexes; avoid full table scans

### Observability

- Structured logging (JSON); correlation IDs
- Metrics: latency (p50, p95, p99), error rate, throughput
- Distributed tracing for microservices
- Alerts on SLO breaches

---

## Operational Excellence

### CI/CD

- Automated tests (unit, integration, e2e)
- Security scanning (SAST, dependency checks)
- Blue-green or canary deployments
- Rollback procedure documented and tested

### Zero-Downtime Deployments

- Health checks before traffic
- Graceful shutdown (drain connections)
- Feature flags for risky changes

### Infrastructure as Code

- Terraform, CloudFormation, or Pulumi
- Separate envs: dev, staging, production

---

## Integration Patterns

| Scenario | Pattern | Notes |
|----------|---------|-------|
| External API | Circuit breaker + retry | Fail fast; fallback when possible |
| Event-driven | Message queue (Kafka, SQS) | At-least-once; idempotent consumers |
| Webhooks | Retry with backoff; verify signature | Handle duplicates |
| Distributed transactions | Saga or eventual consistency | Avoid 2PC when possible |

---

## Code Quality Standards

- SOLID; single responsibility per module
- Unit tests for business logic; integration tests for API/DB
- Clear error handling; never swallow exceptions
- Structured logging with context
- Dependency injection for testability

---

## Additional Resources

For detailed patterns and examples, see [reference.md](reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spock-wen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
