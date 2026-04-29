---
name: openclaw-interchange
description: Shared .md interchange library for OpenClaw skills — atomic writes, deterministic serialization, YAML frontmatter, advisory locking, and schema validation. The foundation all other OpenClaw skills build on. Use when this capability is needed.
metadata:
  author: openclaw
---

# @openclaw/interchange

The shared library that powers agent-to-agent communication via `.md` files.

## Usage

```javascript
import { writeMd, readMd, acquireLock } from '@openclaw/interchange';

// Write an interchange file atomically
await writeMd('ops/status.md', { skill: 'crm', status: 'healthy' }, '## Status\nAll systems go.');

// Read it back
const { meta, content } = readMd('ops/status.md');
```

## Key Features
- Atomic writes (tmp + fsync + rename)
- Deterministic serialization (sorted keys, stable YAML)
- Advisory file locking with stale lock detection
- YAML frontmatter parsing
- Schema validation
- Circuit breaker pattern
- Generation tracking + content hashing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
