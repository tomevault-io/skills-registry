---
name: microservices
description: Microservices architecture patterns and best practices. Service decomposition, inter-service communication, and distributed data management. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Microservices Skill

**Bonded to:** `architecture-patterns-agent` (Secondary)

---

## Quick Start

```bash
# Invoke microservices skill
"Decompose my monolith into microservices"
"Design API gateway for my services"
"Implement Saga pattern for distributed transactions"
```

---

## Decomposition Strategies

| Strategy | Best For | Complexity |
|----------|----------|------------|
| By Business Capability | Clear domains | Medium |
| By Subdomain (DDD) | Complex domains | High |
| By Team | Conway's Law | Medium |
| Strangler Fig | Migration | Low |

---

## Service Decomposition Example

```
E-commerce Monolith → Microservices

├── user-service          # Authentication, profiles
├── product-service       # Catalog, inventory
├── order-service         # Orders, checkout
├── payment-service       # Transactions
├── notification-service  # Email, push, SMS
└── api-gateway           # Routing, auth, rate limiting
```

---

## Communication Patterns

### Synchronous (REST/gRPC)
```python
# Service-to-service call
import httpx

async def get_user_from_user_service(user_id: str):
    async with httpx.AsyncClient() as client:
        response = await client.get(f"http://user-service/users/{user_id}")
        return response.json()
```

### Asynchronous (Events)
```python
# Event publishing
from kafka import KafkaProducer

producer = KafkaProducer(bootstrap_servers=['kafka:9092'])

def publish_order_created(order):
    producer.send('order-events', {
        'type': 'ORDER_CREATED',
        'order_id': order.id,
        'user_id': order.user_id
    })
```

---

## Anti-Patterns to Avoid

| Anti-Pattern | Sign | Solution |
|--------------|------|----------|
| Distributed Monolith | Tight coupling | Define bounded contexts |
| Shared Database | Multiple services, one DB | Database per service |
| Chatty Services | Too many sync calls | Use async messaging |
| Data Inconsistency | No transaction strategy | Implement Saga |

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Cascading failures | No resilience | Circuit breakers |
| Data inconsistency | Distributed tx | Saga pattern |
| High latency | Chatty calls | Batch requests, cache |

---

## Resources

- [Sam Newman - Building Microservices](https://samnewman.io/books/)
- [Microsoft Microservices Architecture](https://docs.microsoft.com/en-us/azure/architecture/microservices/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
