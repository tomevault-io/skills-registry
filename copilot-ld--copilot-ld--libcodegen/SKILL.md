---
name: libcodegen
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libcodegen Skill

## When to Use

- Generating JavaScript types from Protocol Buffer schemas
- Creating gRPC service stubs automatically
- Updating generated code after .proto changes
- Maintaining type consistency across microservices

## Key Concepts

**TypeGenerator**: Generates JavaScript classes from protobuf message
definitions with proper type annotations.

**ServiceGenerator**: Creates gRPC service stubs with method signatures matching
the proto service definitions.

**DefinitionGenerator**: Generates type definitions for IDE support and
documentation.

## Usage Patterns

### Pattern 1: Generate types from protos

```javascript
import { TypeGenerator } from "@copilot-ld/libcodegen";

const generator = new TypeGenerator("./proto");
await generator.generate("./generated/types");
```

### Pattern 2: Generate service stubs

```javascript
import { ServiceGenerator } from "@copilot-ld/libcodegen";

const generator = new ServiceGenerator("./proto");
await generator.generate("./generated/services");
```

## Integration

Run via `make codegen` after modifying .proto files. Output used by libtype and
librpc.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
