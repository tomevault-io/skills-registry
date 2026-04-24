---
name: librpc
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# librpc Skill

## When to Use

- Building gRPC service implementations
- Creating clients for inter-service calls
- Adding authentication to gRPC endpoints
- Implementing distributed tracing across services

## Key Concepts

**RpcServer**: Base server class for gRPC services with middleware support.

**RpcClient**: Base client class with connection management and error handling.

**createClientFactory**: Factory that creates typed service clients with
optional logging and tracing.

## Usage Patterns

### Pattern 1: Create service server

```javascript
import { RpcServer, createService } from "@copilot-ld/librpc";

const service = createService(MyServiceImpl, proto);
const server = new RpcServer([service], config);
await server.start();
```

### Pattern 2: Create service client

```javascript
import { createClientFactory } from "@copilot-ld/librpc";

const factory = createClientFactory(logger, tracer);
const agentClient = factory.createAgentClient("localhost", 50051);
const response = await agentClient.request(message);
```

## Integration

Used by all services for gRPC communication. Works with libtype for message
types.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
