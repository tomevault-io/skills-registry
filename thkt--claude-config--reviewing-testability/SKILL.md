---
name: reviewing-testability
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# Testability Review

## Detection

| ID  | Pattern                      | Fix                             |
| --- | ---------------------------- | ------------------------------- |
| TE1 | Direct `import { db }` usage | Inject dependency as parameter  |
| TE1 | `new Service()` inside class | Constructor injection           |
| TE2 | `fetch()` inside component   | Extract to hook/service, inject |
| TE2 | Mixed side effects + logic   | Separate pure/impure            |
| TE3 | Deep mock chains             | Simplify dependencies           |
| TE4 | Global `config` access       | Pass config as prop/parameter   |
| TE4 | `Date.now()` in logic        | Inject clock/time provider      |
| TE5 | Tight coupling               | Depend on abstractions (DIP)    |

## Criteria

Test setup < 10 lines. No deep mock chains. Dependencies explicit.

## References

| Topic   | File                                                     |
| ------- | -------------------------------------------------------- |
| DI      | `${CLAUDE_SKILL_DIR}/references/dependency-injection.md` |
| Pure    | `${CLAUDE_SKILL_DIR}/references/pure-functions.md`       |
| Mocking | `${CLAUDE_SKILL_DIR}/references/mock-friendly.md`        |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
