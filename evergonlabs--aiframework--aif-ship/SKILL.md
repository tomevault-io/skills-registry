---
name: aif-ship
description: | Use when this capability is needed.
metadata:
  author: evergonlabs
---

# /aif-ship — Shipping Workflow

## Step 0: Telemetry (silent, non-blocking)

```bash
aiframework-telemetry skill_invoked skill=aif-ship 2>/dev/null || true
```

## Step 1: Verify

```bash
cd rust && cargo clippy
cd rust && cargo check
```

## Step 2: Invariant Scan
Check changed files against all invariants in CLAUDE.md.

## Step 3: Doc Sync
Update any documentation affected by the changes.

## Step 4: CHANGELOG + VERSION bump
- Update CHANGELOG.md with user-facing description
- Bump VERSION (PATCH=fix, MINOR=feature, MAJOR=breaking)

## Step 5: Commit (NEVER push without asking the user)

## Step 5.5: Vault Update

If vault/ exists:
- Update `vault/memory/status.md` with shipping details
- If significant architectural decision was made → create ADR in `vault/memory/decisions/`
- Run `vault/.vault/scripts/vault-tools.sh lint`

## Step 6: Report

Output a summary table:
| Step | Status | Details |
|------|--------|---------|
| Lint | PASS/FAIL | ... |
| Type check | PASS/FAIL | ... |
| Tests | PASS/FAIL | X passed, Y failed |
| Build | PASS/FAIL | ... |
| Invariants | PASS/FAIL | ... |
| Docs | SYNCED/NEEDS UPDATE | ... |
| Changelog | UPDATED | ... |
| Committed | YES/NO | commit hash |

---
> Source: [evergonlabs/aiframework](https://github.com/evergonlabs/aiframework) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
