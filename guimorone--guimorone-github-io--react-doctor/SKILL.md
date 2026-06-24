---
name: react-doctor
description: Diagnose and fix React codebase health issues. Use when reviewing React code, fixing performance problems, auditing security, or improving code quality. Use when this capability is needed.
metadata:
  author: guimorone
---

# React Doctor

Scans your React codebase for security, performance, correctness, and architecture issues. Outputs a 0-100 score with actionable diagnostics.

## Usage

From repo root (React app in `website/`):

```bash
npx -y react-doctor@latest website --verbose
```

From `website/`:

```bash
bun run doctor
```

Full health check (knip + lint-fix + doctor):

```bash
bun run check
```

## Workflow

1. Run the command above
2. Read every diagnostic with file paths and line numbers
3. Fix issues starting with errors (highest severity)
4. Re-run to verify the score improved

## Rules (47+)

- **Security**: hardcoded secrets in client bundle, eval()
- **State & Effects**: derived state in useEffect, missing cleanup, useState from props, cascading setState
- **Architecture**: components inside components, giant components, inline render functions
- **Performance**: layout property animations, transition-all, large blur values
- **Correctness**: array index as key, conditional rendering bugs
- **Next.js**: missing metadata, client-side fetching for server data, async client components
- **Bundle Size**: barrel imports, full lodash, moment.js, missing code splitting
- **Server**: missing auth in server actions, blocking without after()
- **Accessibility**: missing prefers-reduced-motion
- **Dead Code**: unused files, exports, types

## Score

- **75+**: Great
- **50-74**: Needs work
- **0-49**: Critical

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guimorone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
