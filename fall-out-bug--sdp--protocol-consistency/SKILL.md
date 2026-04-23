---
name: protocol-consistency
description: Audit consistency across workstream docs, CLI capabilities, and CI workflows. Use when this capability is needed.
metadata:
  author: fall-out-bug
---

# @protocol-consistency

Detect drift between docs, CLI, and CI.

## Workflow

1. **Verify CLI** — `sdp --help`, `sdp <cmd> --help` — commands in docs exist
2. **Validate WS schema** — Read `docs/workstreams/backlog/<ws-id>.md`, run `sdp drift detect <ws-id>`
3. **Validate CI** — `rg "sdp .*" .github/workflows hooks scripts` — paths valid
4. **Report** — Source file, observed vs expected, risk, suggested fix
5. **Track** — `bd create --title="Protocol drift: ..." --type=task --priority=2`

## Output

Report: scope, blocking/non-blocking mismatches, findings, recommended fixes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fall-out-bug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
