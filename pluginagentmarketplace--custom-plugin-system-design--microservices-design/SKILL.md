---
name: microservices-design
description: Production-grade microservices design skill for service decomposition, service mesh, resilience patterns, and observability Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Microservices Design Skill

> **Purpose**: Atomic skill for microservices architecture with comprehensive resilience and observability patterns.

## Skill Identity

| Attribute | Value |
|-----------|-------|
| **Scope** | Decomposition, Resilience, Observability |
| **Responsibility** | Single: Service architecture patterns |
| **Invocation** | `Skill("microservices-design")` |

## Parameter Schema

### Input Validation
```yaml
parameters:
  microservices_context:
    type: object
    required: true
    properties:
      project_type:
        type: string
        enum: [greenfield, monolith_extraction, optimization]
        required: true
      current_state:
        type: object
        properties:
          services: { type: array, items: { type: string } }
          pain_points: { type: array, items: { type: string } }
          team_structure: { type: string }
      requirements:
        type: object
        properties:
          team_size: { type: integer, minimum: 1 }
          deployment_frequency: { type: string, enum: [daily, weekly, monthly] }
          availability_sla: { type: string, pattern: "^\\d{2}\\.\\d+%$" }
          max_latency_ms: { type: integer, minimum: 1 }
      constraints:
        type: object
        properties:
          budget: { type: string }
          timeline: { type: string }
          technology_stack: { type: array, items: { type: string } }

validation_rules:
  - name: "team_size_for_microservices"
    rule: "team_size >= 2"
    warning: "Microservices add overhead; consider monolith for small teams"
  - name: "sla_feasibility"
    rule: "availability_sla <= '99.99%' or has_multi_region"
    warning: "99.99%+ SLA typically requires multi-region deployment"
```

### Output Schema
```yaml
output:
  type: object
  properties:
    service_catalog:
      type: array
      items:
        type: object
        properties:
          name: { type: string }
          responsibility: { type: string }
          api_type: { type: string }
          dependencies: { type: array }
          team_owner: { type: string }
          database: { type: string }
    architecture:
      type: object
      properties:
        communication: { type: object }
        service_mesh: { type: object }
        api_gateway: { type: object }
    resilience:
      type: object
      properties:
        patterns: { type: array }
        configuration: { type: object }
    observability:
      type: object
      properties:
        metrics: { type: array }
        tracing: { type: object }
        logging: { type: object }
        alerting: { type: object }
```

## Core Patterns

### Service Decomposition
```
By Business Capability:
├── Align with business domains
├── Stable boundaries over time
├── Example: Order, Inventory, Payment
└── Team: One team per capability

By Subdomain (DDD):
├── Core: Competitive advantage (build)
├── Supporting: Necessary (build or buy)
├── Generic: Commodity (buy)
└── Bounded Context = Service

By Team (Inverse Conway):
├── Structure services around teams
├── 2-3 services per team (2-pizza)
├── Full ownership model
└── DevOps: You build it, you run it

Anti-Patterns:
├── Distributed Monolith: Tight coupling
├── Nano-services: Too granular
├── Shared Database: Hidden coupling
├── Sync Chains: Latency multiplication
```

### Resilience Patterns
```
Circuit Breaker:
├── States: Closed → Open → Half-Open
├── Config:
│   ├── failure_threshold: 50%
│   ├── slow_call_threshold: 50%
│   ├── wait_duration: 60s
│   └── half_open_calls: 3
├── Implementation: Resilience4j
└── Fallback: Cached data, default, queue

Retry with Backoff:
├── Exponential: delay * 2^attempt
├── Max attempts: 3-5
├── Jitter: ±20%
├── Idempotency: Required
└── Non-retryable: 4xx errors

Bulkhead:
├── Isolate failure domains
├── Thread pool per dependency
├── Semaphore for lightweight
└── Config: maxConcurrentCalls: 25

Timeout:
├── Connection: 1s
├── Read: 5s
├── Total: 10s
└── Cascading: outer > inner
```

### Service Mesh
```
Capabilities:
├── Traffic Management
│   ├── Load balancing
│   ├── Traffic splitting (canary)
│   ├── Circuit breaking
│   └── Retries/timeouts
├── Security
│   ├── mTLS
│   ├── Service identity (SPIFFE)
│   └── Authorization policies
├── Observability
│   ├── Distributed tracing
│   ├── Service metrics
│   └── Access logging
└── Options
    ├── Istio: Full-featured
    ├── Linkerd: Lightweight
    ├── Consul: HashiCorp
    └── AWS App Mesh
```

### Observability (Three Pillars)
```
Metrics:
├── RED: Request, Error, Duration
├── USE: Utilization, Saturation, Errors
├── Key Metrics:
│   ├── http_requests_total{method, path, status}
│   ├── http_request_duration_seconds{quantile}
│   └── http_requests_in_flight
└── Tools: Prometheus, Datadog

Logs:
├── Structured JSON
├── Correlation ID propagation
├── Level: DEBUG, INFO, WARN, ERROR
├── Format:
│   {
│     "timestamp": "ISO8601",
│     "level": "INFO",
│     "service": "order-service",
│     "trace_id": "abc123",
│     "message": "Order created"
│   }
└── Tools: ELK, Loki

Traces:
├── Distributed tracing
├── Span context propagation
├── W3C Trace Context
└── Tools: Jaeger, Zipkin, X-Ray
```

## Retry Logic

### Service Call Retry
```yaml
retry_config:
  http_calls:
    max_attempts: 3
    initial_delay_ms: 100
    max_delay_ms: 5000
    multiplier: 2.0
    jitter_factor: 0.2

  grpc_calls:
    max_attempts: 5
    initial_delay_ms: 50
    max_delay_ms: 2000
    multiplier: 1.5

  retryable:
    - UNAVAILABLE
    - DEADLINE_EXCEEDED
    - RESOURCE_EXHAUSTED
    - 502, 503, 504

  non_retryable:
    - INVALID_ARGUMENT
    - NOT_FOUND
    - ALREADY_EXISTS
    - 400, 401, 403, 404

  idempotency:
    header: "Idempotency-Key"
    required_for: [POST, PATCH]
    cache_ttl: 86400
```

## Logging & Observability

### Log Format
```yaml
log_schema:
  level: { type: string }
  timestamp: { type: string, format: ISO8601 }
  skill: { type: string, value: "microservices-design" }
  event:
    type: string
    enum:
      - service_designed
      - decomposition_planned
      - resilience_configured
      - mesh_deployed
      - sla_defined
  context:
    type: object
    properties:
      service_name: { type: string }
      pattern: { type: string }
      decision: { type: string }

example:
  level: INFO
  event: resilience_configured
  context:
    service_name: payment-service
    pattern: circuit_breaker
    decision: "5 failures in 60s triggers open state"
```

### Metrics
```yaml
metrics:
  - name: service_design_decisions
    type: counter
    labels: [service, decision_type]

  - name: decomposition_services_count
    type: gauge
    labels: [domain]

  - name: resilience_patterns_applied
    type: counter
    labels: [service, pattern]

  - name: sla_target
    type: gauge
    labels: [service]
```

## Troubleshooting

### Common Issues

| Issue | Cause | Resolution |
|-------|-------|------------|
| High latency | Cascade calls | Parallelize, cache |
| Partial failures | No circuit breaker | Add resilience |
| Data inconsistency | Distributed tx | Saga pattern |
| Deployment failures | Coupling | API contracts |
| Debug difficulty | No tracing | Distributed tracing |
| Cascading failures | No bulkhead | Thread isolation |

### Debug Checklist
```
□ Trace ID in all logs?
□ Circuit breakers monitored?
□ Timeouts on all calls?
□ Health checks passing?
□ Service mesh healthy?
□ Dependency graph documented?
□ SLOs defined and measured?
□ Alerting configured?
```

## Unit Test Templates

### Decomposition Tests
```python
# test_microservices_design.py

def test_valid_microservices_context():
    params = {
        "microservices_context": {
            "project_type": "monolith_extraction",
            "current_state": {
                "services": ["monolith"],
                "pain_points": ["slow deployments", "scaling issues"]
            },
            "requirements": {
                "team_size": 15,
                "deployment_frequency": "daily",
                "availability_sla": "99.9%",
                "max_latency_ms": 200
            }
        }
    }
    result = validate_parameters(params)
    assert result.valid == True

def test_small_team_warning():
    params = {
        "microservices_context": {
            "project_type": "greenfield",
            "requirements": {"team_size": 1}
        }
    }
    result = validate_parameters(params)
    assert len(result.warnings) > 0
    assert "overhead" in result.warnings[0]

def test_service_decomposition():
    monolith = {
        "domains": ["users", "orders", "payments", "inventory"],
        "team_size": 12
    }
    result = plan_decomposition(monolith)

    assert len(result.services) == 4
    assert result.services[0].responsibility != ""
    assert result.communication_pattern in ["sync", "async", "mixed"]
```

### Resilience Pattern Tests
```python
def test_circuit_breaker_config():
    service = {"name": "payment-service", "sla": "99.9%"}
    config = generate_circuit_breaker_config(service)

    assert config.failure_rate_threshold == 50
    assert config.wait_duration_in_open_state == 60
    assert config.permitted_calls_in_half_open == 3

def test_timeout_hierarchy():
    services = {
        "gateway": {"timeout": 10000},
        "order": {"timeout": 8000},
        "payment": {"timeout": 5000},
        "db": {"timeout": 2000}
    }
    result = validate_timeout_hierarchy(services)
    assert result.valid == True  # Outer > Inner

def test_invalid_timeout_hierarchy():
    services = {
        "gateway": {"timeout": 5000},
        "order": {"timeout": 10000}  # Child > Parent
    }
    result = validate_timeout_hierarchy(services)
    assert result.valid == False
    assert "hierarchy" in result.errors[0]

def test_bulkhead_sizing():
    service = {
        "name": "inventory-service",
        "expected_concurrency": 100,
        "dependency_latency_ms": 50
    }
    config = calculate_bulkhead_size(service)

    # Thread pool sized for expected load + buffer
    assert config.max_concurrent_calls >= 100
    assert config.max_wait_duration_ms <= 1000
```

### SLA Calculation Tests
```python
def test_serial_availability():
    services = [0.999, 0.999, 0.999]  # Three 9s each
    result = calculate_serial_availability(services)
    assert abs(result - 0.997) < 0.001  # ~99.7%

def test_parallel_availability():
    replicas = [0.999, 0.999]  # Two replicas
    result = calculate_parallel_availability(replicas)
    assert abs(result - 0.999999) < 0.000001  # ~99.9999%

def test_sla_achievability():
    result = check_sla_achievable(
        target_sla="99.99%",
        service_count=5,
        per_service_availability=0.9999,
        has_redundancy=True
    )
    assert result.achievable == True
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2025-01 | Production-grade rewrite with resilience patterns |
| 1.0.0 | 2024-12 | Initial release |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
