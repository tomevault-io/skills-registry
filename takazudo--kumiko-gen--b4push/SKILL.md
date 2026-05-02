---
name: b4push
description: >- Use when this capability is needed.
metadata:
  author: takazudo
---

# Before Push Check

Run `pnpm b4push` from the project root. This executes `scripts/run-b4push.sh` which runs all checks in order:

1. Build kumiko-gen package
2. Test kumiko-gen package
3. Build kumiko-gen-viewer
4. Doc data generation - doc-titles.json + category-nav.json
5. Doc site build - Full Docusaurus production build

All steps must pass.

## On failure

1. Read the failure output to identify which step failed
2. Fix the issue
3. Re-run `pnpm b4push` to confirm all checks pass
4. Report the final status

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/takazudo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
