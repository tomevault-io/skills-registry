---
name: libstorage
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libstorage Skill

## When to Use

- Persisting data to filesystem or cloud storage
- Supporting multiple storage backends
- Reading/writing JSON and JSONL files
- Building storage-agnostic applications

## Key Concepts

**Storage interface**: Provides a common API (read, write, list, delete) for all
supported storage systems.

**createStorage**: Factory that returns appropriate backend based on environment
configuration.

**JSONL support**: Parse and serialize newline-delimited JSON for streaming
data.

## Usage Patterns

### Pattern 1: Create storage instance

```javascript
import { createStorage } from "@copilot-ld/libstorage";

const storage = createStorage(config);
await storage.write("data/file.json", { key: "value" });
const data = await storage.read("data/file.json");
```

### Pattern 2: JSONL operations

```javascript
import { parseJsonl, serializeJsonl } from "@copilot-ld/libstorage";

const items = parseJsonl(jsonlContent);
const output = serializeJsonl(items);
```

## Integration

Used by all indexes and services. Backend configured via STORAGE environment.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
