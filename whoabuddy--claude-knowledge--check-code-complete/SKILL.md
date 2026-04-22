---
name: check-code-complete
description: Verify code has no TODOs, stub implementations, or dead code before merge Use when this capability is needed.
metadata:
  author: whoabuddy
---

# Check Code Complete Skill

Scan codebase for incomplete work that shouldn't be merged.

## Usage

```bash
/check-code-complete           # Check current directory
/check-code-complete ./src     # Check specific path
```

## What Gets Checked

| Category | Examples |
|----------|----------|
| TODOs | `TODO:`, `FIXME:`, `HACK:`, `XXX:` |
| Stubs | Empty functions, `throw "not implemented"` |
| Dead code | Commented blocks, unused imports/vars |
| Debug artifacts | `console.log`, `debugger`, `.only()` |

## Output

List of issues found with file:line references and summary count.

## Runbook

Full procedure: `runbook/check-code-complete.md` in your knowledge base.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/whoabuddy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
