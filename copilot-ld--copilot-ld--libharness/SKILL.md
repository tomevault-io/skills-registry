---
name: libharness
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libharness Skill

## When to Use

- Writing unit tests for services and packages
- Mocking external dependencies (storage, config, logging)
- Creating isolated test environments
- Testing gRPC service implementations

## Key Concepts

**Framework mocks**: createMockStorage, createMockConfig, createMockLogger
provide test doubles for core infrastructure.

**Client mocks**: createMockMemoryClient, createMockLlmClient, etc. provide test
doubles for gRPC service clients.

**Fixtures**: Pre-configured test data and assertion helpers.

## Usage Patterns

### Pattern 1: Mock infrastructure

```javascript
import {
  createMockConfig,
  createMockStorage,
  createMockLogger,
} from "@copilot-ld/libharness";

const config = createMockConfig("test-service");
const storage = createMockStorage();
const logger = createMockLogger();
```

### Pattern 2: Mock service clients

```javascript
import { createMockLlmClient } from "@copilot-ld/libharness";

const llmClient = createMockLlmClient({
  completionResponse: { content: "Hello" },
});
```

## Integration

Used across all test files. Requires libtype for generated type mocks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
