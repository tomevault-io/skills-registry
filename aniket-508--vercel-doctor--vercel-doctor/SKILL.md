---
name: vercel-doctor
description: Find ways to cut your Vercel bill. Run after making changes to catch cost-heavy patterns early in Next.js projects. Use when this capability is needed.
metadata:
  author: Aniket-508
---

# Vercel Doctor

Scans your Next.js codebase for patterns that drive up your Vercel bill, focusing on compute duration, function invocations, and bandwidth optimization.

## Usage

```bash
npx -y vercel-doctor@latest . --verbose --diff
```

## Workflow

Run after making changes to catch cost-heavy patterns early. Focus on fixing issues that reduce function execution time and invocations first.

---
> Source: [Aniket-508/vercel-doctor](https://github.com/Aniket-508/vercel-doctor) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
