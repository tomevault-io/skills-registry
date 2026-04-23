---
name: error-translator
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Error Translator

## Commands

- `/error <paste error message>` — Translate and suggest fix
- `/error --context <file>` — Translate with file context for better suggestions

## Procedure

### Phase 1: Parse

1. Identify error source (language, framework, service)
2. Extract error code, message, stack trace
3. Identify root cause line (first non-framework line in stack)

### Phase 2: Translate

Produce plain-English explanation: what happened, why, and where (file:line).

### Phase 3: Fix

Provide: immediate fix (exact code/command), verification step, prevention strategy.

## Error Pattern Library

### Power Platform / Dataverse

- "Primary Attribute Display Name not specified" — Missing displayname in displaynames element. Fix: add displayname tag.
- "Option set display name not specified" — Inline OptionSet missing displaynames block. Fix: add displaynames/displayname to OptionSet.
- "Failed to find connection references" — Workflow refs connection not in solution. Fix: add connectionreferences section to customizations.xml.
- "ENV_IP_0002 unsupported schema version" — Non-fatal Cloud Flow warning. Flows may need manual activation.

### Python

- ModuleNotFoundError — Package not installed. Fix: pip install.
- KeyError — Dictionary key missing. Fix: use dict.get() or check membership.
- TypeError NoneType not subscriptable — Variable is None. Fix: add None check.
- ConnectionRefusedError — Service down or wrong port. Fix: verify service and URL.

### JavaScript/TypeScript

- Cannot read properties of undefined — Accessing property on nonexistent object. Fix: optional chaining or null check.
- Unexpected token — Invalid JSON/JS syntax. Fix: check trailing commas, quotes, encoding.
- ERR_MODULE_NOT_FOUND — Bad import path. Fix: verify file exists and path is correct.

### HTTP

- 401: Auth missing/invalid. 403: Authenticated but unauthorized. 404: Resource not found. 429: Rate limited. 500: Server error.

### Git

- "not a git repository" — Wrong directory. Fix: cd to repo root.
- "failed to push some refs" — Remote ahead. Fix: git pull --rebase.
- "CONFLICT" — Merge conflict. Fix: resolve markers, git add.

## MCMAP Error Recovery

For pac solution import errors, also check memory/context/errors.md, displayname tags, connection references, and OptionSet prefix conventions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
