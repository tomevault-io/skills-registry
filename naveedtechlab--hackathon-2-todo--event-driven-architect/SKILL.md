---
name: event-driven-architect
description: Design and implement event-driven cloud architectures with Kafka topics, Dapr pub/sub, and scalable microservice patterns. Use when designing distributed systems, implementing async messaging, creating event-based workflows, or building loosely coupled services. Covers fault-tolerant, cloud-portable architectures. Use when this capability is needed.
metadata:
  author: naveedtechlab
---

# Event-Driven Architect

Design scalable, loosely coupled event-driven architectures for cloud-native applications.

## Core Principles

1. **Loose coupling** - Services communicate via events, not direct calls
2. **Fault tolerance** - Failures don't cascade; retry and compensate
3. **Cloud portability** - Abstract messaging via Dapr; switch brokers without code changes
4. **Event sourcing mindset** - Events are the source of truth

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                         Services                             │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐        │
│  │  Todo   │  │  User   │  │ Notify  │  │Analytics│        │
│  │ Service │  │ Service │  │ Service │  │ Service │        │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘        │
│       │            │            │            │              │
│       └────────────┴────────────┴────────────┘              │
│                         │                                    │
│                    ┌────┴────┐                              │
│                    │  Dapr   │  ← Cloud-agnostic abstraction│
│                    │ Sidecar │                              │
│                    └────┬────┘                              │
└─────────────────────────┼───────────────────────────────────┘
                          │
                    ┌─────┴─────┐
                    │Event Bus  │  ← Kafka / Redis / Cloud PubSub
                    └───────────┘
```

## Event Design

### Naming Convention

```
<domain>.<entity>.<action>

Examples:
  todos.task.created
  todos.task.completed
  users.account.registered
```

### Event Schema (CloudEvents)

```json
{
  "specversion": "1.0",
  "id": "uuid",
  "source": "/todos/api",
  "type": "todos.task.created",
  "time": "2024-01-15T10:30:00Z",
  "data": {
    "todoId": "123",
    "userId": "456",
    "title": "Buy groceries"
  }
}
```

## Todo Event Workflow

```
User creates todo
       │
       ▼
┌──────────────┐     todos.task.created
│ Todo Service │─────────────────────────┐
└──────────────┘                         │
                                         ▼
                    ┌────────────────────────────────────────┐
                    │              Event Bus                  │
                    └────────────────────────────────────────┘
                         │                    │
                         ▼                    ▼
               ┌──────────────┐     ┌──────────────┐
               │ Notification │     │  Analytics   │
               │   Service    │     │   Service    │
               └──────────────┘     └──────────────┘
```

## Dapr Pub/Sub (Recommended)

### Publish Event

```python
from dapr.clients import DaprClient

async def publish_todo_created(todo: Todo):
    with DaprClient() as client:
        client.publish_event(
            pubsub_name="pubsub",
            topic_name="todos.task.created",
            data=json.dumps({"todoId": str(todo.id), "title": todo.title})
        )
```

### Subscribe to Events

```python
@app.get("/dapr/subscribe")
async def subscribe():
    return [
        {"pubsubname": "pubsub", "topic": "todos.task.created", "route": "/events/todo-created"}
    ]

@app.post("/events/todo-created")
async def handle_todo_created(request: Request):
    event = await request.json()
    await send_notification(event["data"]["userId"], "Todo created!")
    return {"status": "SUCCESS"}
```

### Component Config

```yaml
# components/pubsub.yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: pubsub
spec:
  type: pubsub.kafka  # or pubsub.redis for local dev
  version: v1
  metadata:
    - name: brokers
      value: "kafka:9092"
```

## Key Patterns

### Transactional Outbox

Ensure event publishing doesn't fail separately from database writes:

```sql
BEGIN;
  INSERT INTO todos (...) VALUES (...);
  INSERT INTO outbox (event_type, payload) VALUES ('todos.task.created', '...');
COMMIT;
-- Separate relay publishes from outbox to Kafka
```

### Idempotent Consumer

```python
@app.post("/events/todo-created")
async def handle_todo_created(request: Request):
    event = await request.json()
    event_id = event["id"]

    if await is_already_processed(event_id):
        return {"status": "SUCCESS"}  # Skip duplicate

    await process_event(event["data"])
    await mark_processed(event_id)

    return {"status": "SUCCESS"}
```

### Dead Letter Queue

```python
@app.post("/events/todo-created")
async def handle_event(request: Request):
    try:
        await process_event(await request.json())
        return {"status": "SUCCESS"}
    except RetryableError:
        return {"status": "RETRY"}   # Dapr retries
    except Exception:
        return {"status": "DROP"}    # Send to DLQ
```

## Service Communication

| Pattern | Use For | Example |
|---------|---------|---------|
| Events (async) | Notifications, analytics, sync | Todo created → notify |
| HTTP (sync) | Auth checks, validation | Verify user exists |
| Saga | Multi-step transactions | Order → Payment → Ship |

## Resilience Checklist

- [ ] Idempotent event handlers (handle duplicates)
- [ ] Dead letter queues for failed events
- [ ] Circuit breakers for sync calls
- [ ] Retry with exponential backoff
- [ ] Graceful degradation (return defaults on failure)
- [ ] Health checks for all services

## References

- [references/kafka-patterns.md](references/kafka-patterns.md) - Topic design, producers, consumers, error handling
- [references/dapr-pubsub.md](references/dapr-pubsub.md) - Component config, publish/subscribe, local development
- [references/microservice-patterns.md](references/microservice-patterns.md) - Saga, CQRS, circuit breaker, bulkhead

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/naveedtechlab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
