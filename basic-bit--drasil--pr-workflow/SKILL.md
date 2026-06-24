---
name: pr-workflow
description: Land changes via PRs with CI + AI review loops (issue -> branch/worktree -> PR). Use when this capability is needed.
metadata:
  author: basic-bit
---

## Reference

- Canonical workflow: `docs/dev/pr-workflow.md`
- PR template: `.github/pull_request_template.md`
- Worktrees: `docs/dev/worktrees.md`

## Quick local gate

```bash
npm ci
npm run format:check
npm run check
```

## Merge readiness

- Required checks pass.
- PR review threads are resolved (or explicitly addressed).

## Notes

- Never paste secrets into PRs.
- If build fails due to missing Prisma client types, run `npm run prisma:generate`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/basic-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
