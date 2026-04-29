---
name: event-driven
description: Production-grade event-driven architecture skill for Kafka, RabbitMQ, event sourcing, CQRS, and message patterns Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Event-Driven Skill

> **Purpose**: Atomic skill for event-driven architecture with comprehensive messaging patterns and delivery guarantees.

## Skill Identity

| Attribute | Value |
|-----------|-------|
| **Scope** | Kafka, RabbitMQ, Event Sourcing, CQRS |
| **Responsibility** | Single: Async messaging and event patterns |
| **Invocation** | `Skill("event-driven")` |

## Parameter Schema

### Input Validation
```yaml
parameters:
  event_context:
    type: object
    required: true
    properties:
      use_case:
        type: string
        enum: [pub_sub, work_queue, event_sourcing, cqrs, saga]
        required: true
      requirements:
        type: object
        required: true
        properties:
          throughput:
            type: string
            pattern: "^\\d+[KM]?\\s*msg/s$"
          latency:
            type: string
            pattern: "^\\d+\\s*(ms|s)$"
          ordering:
            type: string
            enum: [none, partition, global]
          delivery:
            type: string
            enum: [at_most_once, at_least_once, exactly_once]
          retention:
            type: string
            pattern: "^\\d+\\s*(hours?|days?|weeks?)$"
      producers:
        type: array
        items: { type: string }
        minItems: 1
      consumers:
        type: array
        items: { type: string }
        minItems: 1

validation_rules:
  - name: "exactly_once_complexity"
    rule: "delivery == 'exactly_once' implies warn_complexity"
    warning: "Exactly-once requires idempotent consumers"
  - name: "global_ordering_throughput"
    rule: "ordering == 'global' implies throughput <= '10K msg/s'"
    warning: "Global ordering limits throughput to single partition"
```

### Output Schema
```yaml
output:
  type: object
  properties:
    broker:
      type: object
      properties:
        technology: { type: string }
        rationale: { type: string }
    topology:
      type: object
      properties:
        topics: { type: array }
        partitions: { type: integer }
        replication_factor: { type: integer }
    message_schema:
      type: object
      properties:
        format: { type: string }
        schema: { type: object }
        versioning: { type: string }
    consumer_config:
      type: object
      properties:
        group_strategy: { type: string }
        error_handling: { type: string }
        dlq: { type: object }
```

## Core Patterns

### Broker Selection
```
Kafka:
├── Throughput: 1M+ msg/s
├── Latency: ~5ms
├── Ordering: Per-partition
├── Retention: Configurable (days/forever)
├── Replay: ✅ Full log replay
├── Use: Event sourcing, streaming
└── Complexity: High

RabbitMQ:
├── Throughput: 50K msg/s
├── Latency: ~1ms
├── Ordering: Per-queue
├── Retention: Until acknowledged
├── Replay: ❌ No native replay
├── Use: Work queues, routing
└── Complexity: Medium

AWS SQS:
├── Throughput: Unlimited (managed)
├── Latency: ~50ms
├── Ordering: FIFO optional
├── Retention: 14 days max
├── Replay: ❌ No replay
├── Use: Serverless, simple queues
└── Complexity: Low

Pulsar:
├── Throughput: 1M+ msg/s
├── Latency: ~5ms
├── Ordering: Per-partition
├── Retention: Tiered storage
├── Replay: ✅ Full replay
├── Use: Multi-tenancy
└── Complexity: High
```

### Message Patterns
```
Pub/Sub:
├── Many consumers per message
├── Topic-based routing
├── Decoupled producers/consumers
└── Use: Notifications, events

Work Queue:
├── One consumer per message
├── Load balancing
├── Acknowledgments
└── Use: Background jobs

Request/Reply:
├── Synchronous over async
├── Correlation IDs
├── Timeout handling
└── Use: RPC-like patterns

Dead Letter Queue:
├── Failed message storage
├── Retry mechanism
├── Manual review
└── Use: Error handling
```

### Event Sourcing
```
Event Store:
├── Append-only log
├── Events are immutable
├── State = replay(events)
└── Snapshots for performance

Event Schema:
{
  "event_id": "uuid-v4",
  "event_type": "OrderPlaced",
  "aggregate_id": "order-123",
  "aggregate_version": 5,
  "timestamp": "2025-01-01T00:00:00Z",
  "payload": { ... },
  "metadata": {
    "correlation_id": "uuid",
    "causation_id": "uuid",
    "user_id": "user-456"
  }
}

Projections:
├── Materialize events to read models
├── Async for eventual consistency
├── Rebuild by replaying events
└── Catch-up from position
```

### CQRS Pattern
```
Command Side:
├── Validate command
├── Load aggregate state
├── Apply business rules
├── Emit events
└── Persist to event store

Query Side:
├── Subscribe to events
├── Update projections
├── Serve optimized queries
└── Different DBs per query type

Benefits:
├── Independent scaling
├── Optimized read models
├── Full audit trail
└── Time travel (replay)

Trade-offs:
├── Eventual consistency
├── Increased complexity
├── Event versioning
└── Debugging challenges
```

## Retry Logic

### Consumer Retry Configuration
```yaml
retry_config:
  message_processing:
    max_attempts: 5
    initial_delay_ms: 1000
    max_delay_ms: 60000
    multiplier: 2.0
    jitter_factor: 0.2

  dead_letter:
    enabled: true
    topic_suffix: ".dlq"
    retention_days: 7
    alert_threshold: 100

  error_classification:
    retryable:
      - TIMEOUT
      - SERVICE_UNAVAILABLE
      - RATE_LIMITED
      - DATABASE_DEADLOCK
    non_retryable:
      - VALIDATION_ERROR
      - DUPLICATE_MESSAGE
      - SCHEMA_MISMATCH

  circuit_breaker:
    failure_threshold: 10
    reset_timeout_seconds: 60
    half_open_attempts: 1
```

## Logging & Observability

### Log Format
```yaml
log_schema:
  level: { type: string }
  timestamp: { type: string, format: ISO8601 }
  skill: { type: string, value: "event-driven" }
  event:
    type: string
    enum:
      - message_produced
      - message_consumed
      - message_acked
      - message_nacked
      - message_dlq
      - consumer_lag
      - rebalance
  context:
    type: object
    properties:
      topic: { type: string }
      partition: { type: integer }
      offset: { type: integer }
      consumer_group: { type: string }
      latency_ms: { type: number }

example:
  level: INFO
  event: message_consumed
  context:
    topic: orders.placed
    partition: 3
    offset: 12345
    consumer_group: order-service
    latency_ms: 45
```

### Metrics
```yaml
metrics:
  - name: messages_produced_total
    type: counter
    labels: [topic, partition]

  - name: messages_consumed_total
    type: counter
    labels: [topic, consumer_group, status]

  - name: consumer_lag
    type: gauge
    labels: [topic, consumer_group, partition]

  - name: message_processing_duration_seconds
    type: histogram
    labels: [topic, consumer_group]
    buckets: [0.001, 0.01, 0.1, 1, 10]

  - name: dlq_messages_total
    type: counter
    labels: [topic, error_type]
```

## Troubleshooting

### Common Issues

| Issue | Cause | Resolution |
|-------|-------|------------|
| Consumer lag | Slow processing | Scale, optimize |
| Message loss | Acks misconfigured | Enable confirms |
| Duplicates | At-least-once | Idempotent consumers |
| Ordering issues | Wrong partition key | Fix key selection |
| DLQ overflow | Processing bugs | Fix root cause |
| Rebalance storms | Unstable consumers | Increase session timeout |

### Debug Checklist
```
□ Producer acks configured?
□ Consumer offsets committed?
□ Replication factor >= 3?
□ Consumer lag monitored?
□ DLQ alerts configured?
□ Idempotency implemented?
□ Schema registry in use?
```

## Unit Test Templates

### Message Flow Tests
```python
# test_event_driven.py

def test_valid_event_context():
    params = {
        "event_context": {
            "use_case": "pub_sub",
            "requirements": {
                "throughput": "10K msg/s",
                "latency": "100ms",
                "ordering": "partition",
                "delivery": "at_least_once",
                "retention": "7 days"
            },
            "producers": ["order-service"],
            "consumers": ["notification-service", "analytics-service"]
        }
    }
    result = validate_parameters(params)
    assert result.valid == True

def test_global_ordering_throughput_warning():
    params = {
        "event_context": {
            "requirements": {
                "throughput": "100K msg/s",
                "ordering": "global"  # Incompatible
            }
        }
    }
    result = validate_parameters(params)
    assert len(result.warnings) > 0
    assert "throughput" in result.warnings[0]

def test_partition_count_calculation():
    result = calculate_partitions(
        throughput_per_second=10000,
        max_throughput_per_partition=1000,
        replication_factor=3
    )
    assert result.partitions >= 10
    assert result.partitions % 2 == 0  # Even for balancing
```

### Idempotency Tests
```python
def test_idempotent_consumer():
    consumer = IdempotentConsumer(dedup_store=MockRedis())

    message = {"id": "msg-123", "data": "test"}

    # First processing
    result1 = consumer.process(message)
    assert result1.processed == True
    assert result1.duplicate == False

    # Duplicate processing
    result2 = consumer.process(message)
    assert result2.processed == False
    assert result2.duplicate == True

def test_exactly_once_with_transaction():
    producer = TransactionalProducer()

    with producer.transaction():
        producer.send("topic-a", {"key": "value"})
        producer.send("topic-b", {"key": "value"})
        # Both messages committed atomically

    assert producer.committed_count == 2

def test_dlq_routing():
    consumer = ConsumerWithDLQ(
        dlq_topic="orders.dlq",
        max_retries=3
    )

    # Simulate processing failure
    message = {"id": "msg-123", "data": "invalid"}
    for _ in range(3):
        result = consumer.process(message)
        assert result.success == False

    # After 3 failures, should be in DLQ
    assert consumer.dlq_messages[-1]["id"] == "msg-123"
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2025-01 | Production-grade rewrite with CQRS patterns |
| 1.0.0 | 2024-12 | Initial release |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
