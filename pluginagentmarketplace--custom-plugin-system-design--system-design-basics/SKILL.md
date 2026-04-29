---
name: system-design-basics
description: Production-grade system design fundamentals skill for scalability, reliability, availability patterns, and architectural decision-making Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# System Design Basics Skill

> **Purpose**: Atomic skill for system design fundamentals with validated parameters and production-ready patterns.

## Skill Identity

| Attribute | Value |
|-----------|-------|
| **Scope** | Scalability, Reliability, Availability |
| **Responsibility** | Single: Foundational design patterns |
| **Invocation** | `Skill("system-design-basics")` |

## Parameter Schema

### Input Validation
```yaml
parameters:
  design_context:
    type: object
    required: true
    properties:
      problem:
        type: string
        minLength: 10
        maxLength: 500
        description: "Clear problem statement"
      scale:
        type: object
        properties:
          users: { type: integer, minimum: 0 }
          qps: { type: integer, minimum: 0 }
          data_size: { type: string, pattern: "^\\d+[KMGTP]B$" }
        required: [users]
      constraints:
        type: array
        items: { type: string }
        maxItems: 10

validation_rules:
  - name: "scale_consistency"
    rule: "qps <= users * 100"
    error: "QPS seems unreasonably high for user count"
  - name: "required_context"
    rule: "problem.length >= 10"
    error: "Problem statement too vague"
```

### Output Schema
```yaml
output:
  type: object
  properties:
    architecture:
      type: object
      properties:
        components: { type: array }
        data_flow: { type: string }
        trade_offs: { type: array }
    capacity:
      type: object
      properties:
        storage: { type: string }
        bandwidth: { type: string }
        compute: { type: string }
    recommendations:
      type: array
      items:
        type: object
        properties:
          area: { type: string }
          suggestion: { type: string }
          priority: { type: string, enum: [high, medium, low] }
```

## Core Patterns

### Scalability Patterns
```
Horizontal Scaling:
├── Stateless Services
│   ├── No session affinity
│   ├── Externalize state (Redis, DB)
│   └── Container-ready design
├── Load Balancing
│   ├── Round-robin (simple)
│   ├── Least connections (dynamic)
│   ├── IP hash (session affinity)
│   └── Weighted (capacity-aware)
├── Database Scaling
│   ├── Read replicas
│   ├── Sharding (horizontal)
│   └── Caching layer
└── Queue-Based Scaling
    ├── Decouple producers/consumers
    ├── Handle load spikes
    └── Enable async processing

Vertical Scaling:
├── CPU optimization
├── Memory increase
├── SSD storage
└── Network bandwidth
```

### Reliability Patterns
```
Redundancy:
├── Active-passive failover
├── Active-active (multi-master)
├── N+1 redundancy
└── Geographic distribution

Fault Tolerance:
├── Circuit breaker
│   └── Prevent cascade failures
├── Retry with backoff
│   └── Transient error recovery
├── Bulkhead isolation
│   └── Failure containment
├── Timeout enforcement
│   └── Resource protection
└── Graceful degradation
    └── Partial functionality
```

### Availability Calculations
```
Availability Formula:
├── Availability = MTBF / (MTBF + MTTR)
├── MTBF: Mean Time Between Failures
└── MTTR: Mean Time To Repair

Availability Tiers:
├── 99% (two 9s) = 3.65 days/year downtime
├── 99.9% (three 9s) = 8.76 hours/year
├── 99.99% (four 9s) = 52.56 minutes/year
├── 99.999% (five 9s) = 5.26 minutes/year
└── 99.9999% (six 9s) = 31.5 seconds/year

Serial vs Parallel:
├── Serial: A_total = A1 × A2 × A3
│   └── 99.9% × 99.9% × 99.9% = 99.7%
└── Parallel: A_total = 1 - (1-A1) × (1-A2)
    └── 1 - (0.001 × 0.001) = 99.9999%
```

## Retry Logic

### Exponential Backoff Configuration
```yaml
retry_config:
  max_attempts: 5
  initial_delay_ms: 100
  max_delay_ms: 30000
  multiplier: 2.0
  jitter_factor: 0.2

  retry_on:
    - TIMEOUT
    - RATE_LIMITED
    - SERVICE_UNAVAILABLE
    - NETWORK_ERROR

  abort_on:
    - VALIDATION_ERROR
    - NOT_FOUND
    - UNAUTHORIZED

implementation:
  delay = min(initial_delay * (multiplier ^ attempt), max_delay)
  jittered_delay = delay * (1 + random(-jitter_factor, jitter_factor))
```

## Logging & Observability

### Log Format
```yaml
log_schema:
  level: { type: string, enum: [DEBUG, INFO, WARN, ERROR] }
  timestamp: { type: string, format: ISO8601 }
  skill: { type: string, value: "system-design-basics" }
  correlation_id: { type: string }
  event:
    type: string
    enum:
      - skill_invoked
      - parameter_validated
      - pattern_applied
      - calculation_performed
      - skill_completed
      - error_occurred
  context: { type: object }
  duration_ms: { type: number }

example:
  level: INFO
  timestamp: "2025-01-01T00:00:00.000Z"
  skill: "system-design-basics"
  correlation_id: "abc123"
  event: "pattern_applied"
  context:
    pattern: "horizontal_scaling"
    target: "api_servers"
  duration_ms: 45
```

### Metrics
```yaml
metrics:
  - name: skill_invocation_count
    type: counter
    labels: [status, pattern_type]

  - name: skill_duration_seconds
    type: histogram
    buckets: [0.1, 0.5, 1, 2, 5]

  - name: parameter_validation_failures
    type: counter
    labels: [validation_rule]

  - name: pattern_usage
    type: counter
    labels: [pattern_name]
```

## Troubleshooting

### Common Issues

| Issue | Cause | Resolution |
|-------|-------|------------|
| Vague output | Unclear input | Ask for specific scale/constraints |
| Over-engineered | Premature optimization | Apply YAGNI, start simple |
| Under-scaled | Missing growth projections | Request 5-year growth estimate |
| Inconsistent trade-offs | Missing context | Clarify priority (cost/perf/availability) |

### Debug Checklist
```
□ Input parameters validated?
□ Scale requirements clear?
□ Constraints explicitly stated?
□ Trade-offs documented?
□ Capacity estimates calculated?
□ Failure modes considered?
```

## Unit Test Templates

### Parameter Validation Tests
```python
# test_system_design_basics.py

def test_valid_parameters():
    params = {
        "design_context": {
            "problem": "Design URL shortener for 100M users",
            "scale": {"users": 100000000, "qps": 10000},
            "constraints": ["low latency", "high availability"]
        }
    }
    result = validate_parameters(params)
    assert result.valid == True

def test_invalid_problem_too_short():
    params = {
        "design_context": {
            "problem": "Short",
            "scale": {"users": 1000}
        }
    }
    result = validate_parameters(params)
    assert result.valid == False
    assert "minLength" in result.errors[0]

def test_qps_scale_consistency():
    params = {
        "design_context": {
            "problem": "High QPS test case",
            "scale": {"users": 100, "qps": 1000000}
        }
    }
    result = validate_parameters(params)
    assert result.warnings[0] == "QPS seems unreasonably high"

def test_capacity_calculation():
    result = calculate_capacity(
        users=1000000,
        data_per_user="1KB",
        growth_rate=0.1
    )
    assert result.storage == "1.1GB"  # With 10% growth buffer
```

### Pattern Application Tests
```python
def test_horizontal_scaling_pattern():
    context = {"users": 1000000, "stateless": True}
    result = apply_pattern("horizontal_scaling", context)
    assert "load_balancer" in result.components
    assert "auto_scaling_group" in result.components

def test_availability_calculation():
    components = [0.999, 0.999, 0.999]  # 99.9% each
    serial = calculate_serial_availability(components)
    assert abs(serial - 0.997) < 0.001  # ~99.7%

    parallel = calculate_parallel_availability([0.999, 0.999])
    assert abs(parallel - 0.999999) < 0.000001  # ~99.9999%
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2025-01 | Production-grade rewrite with validation schemas |
| 1.0.0 | 2024-12 | Initial release |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
