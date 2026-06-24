---
name: libutil
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libutil Skill

## When to Use

- Counting tokens for LLM context management
- Generating hashes and UUIDs
- Finding project root directory
- Running child processes
- Managing generated code bundles

## Key Concepts

**Token counting**: GPT-compatible tokenization for context window management.

**Project root**: Finds repository root by looking for package.json markers.

**Bundle management**: Download and extract pre-generated code bundles.

## Usage Patterns

### Pattern 1: Count tokens

```javascript
import { countTokens, estimateTokens } from "@copilot-ld/libutil";

const exact = countTokens("Hello, world!"); // Accurate count
const fast = estimateTokens("Hello, world!"); // Quick estimate
```

### Pattern 2: Generate identifiers

```javascript
import { generateHash, generateUuid } from "@copilot-ld/libutil";

const hash = generateHash(content); // 16-char SHA256
const uuid = generateUuid(); // Standard UUID
```

### Pattern 3: Find project root

```javascript
import { findProjectRoot } from "@copilot-ld/libutil";

const root = await findProjectRoot(); // /path/to/copilot-ld
```

## Integration

Used across packages for common utilities. countTokens used by libmemory.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
