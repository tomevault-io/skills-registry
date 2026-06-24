---
name: benchmark-quick
description: Quick smoke test of the full mdarena pipeline, mine 3 PRs from a repo and run 1 task. Use when this capability is needed.
metadata:
  author: HudsonGri
---

Run a quick end-to-end smoke test of the mdarena pipeline. Use $ARGUMENTS as the GitHub repo (e.g., `owner/repo`). If no repo is provided, ask the user for one.

Steps:

1. `uv run mdarena mine $ARGUMENTS --limit 3 -o /tmp/mdarena-smoke/tasks`
2. Verify the manifest.json was created and has tasks
3. Report the number of tasks mined and their difficulty breakdown
4. Note: skip `mdarena run` (requires Claude CLI budget), just verify mining works

Report what was mined: task count, PR numbers, difficulty distribution, whether test commands were detected.

---
> Source: [HudsonGri/mdarena](https://github.com/HudsonGri/mdarena) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
