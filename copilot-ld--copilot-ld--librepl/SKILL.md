---
name: librepl
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# librepl Skill

## When to Use

- Building interactive CLI tools
- Creating debug and exploration interfaces
- Adding REPL functionality to applications
- Prototyping with persistent state

## Key Concepts

**Repl**: Interactive command-line interface with custom commands, history, and
state management.

## Usage Patterns

### Pattern 1: Create custom REPL

```javascript
import { Repl } from "@copilot-ld/librepl";

const repl = new Repl({
  prompt: "agent> ",
  commands: {
    search: async (query) => {
      const results = await searchIndex(query);
      return JSON.stringify(results, null, 2);
    },
    help: () => "Commands: search <query>, help, exit",
  },
});

await repl.start();
```

### Pattern 2: State persistence

```javascript
const repl = new Repl({
  prompt: "> ",
  storage: storage,
  stateKey: "repl-state",
});
// State persists across sessions
```

## Integration

Used by CLI tools like cli-chat and cli-search. Integrates with libformat for
terminal output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
