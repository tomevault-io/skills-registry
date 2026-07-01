---
name: custom-codereview-guide
description: >- Use when this capability is needed.
metadata:
  author: OpenHands
---

# OpenHands CLI — Custom Code Review Guidelines

## Version Bump PRs — Auto-Approve Policy

When a PR exclusively bumps SDK package versions (e.g., `openhands-sdk`,
`openhands-tools`, or other OpenHands SDK packages), **always approve it**
by submitting an actual GitHub approval review via the API.

Detection criteria — the PR satisfies **all** of the following:
1. The title or branch name contains "bump", "version bump", or "sdk" version references.
2. The diff only touches dependency-related files (`pyproject.toml`, `uv.lock`)
   with version number changes for `openhands-sdk` and/or `openhands-tools`.
3. No application source code, tests, configuration, or workflow files are modified.

When these criteria are met:
- Skip the full code review analysis (data structures, complexity, pragmatism, etc.).
- Do **not** require an `Evidence` section in the PR description.
- Assign 🟢 **Good taste** and 🟢 LOW risk.
- **Submit an APPROVE review via the GitHub API** (not just a comment). Use:
  ```bash
  gh api repos/{owner}/{repo}/pulls/{pr_number}/reviews \
    -f event="APPROVE" \
    -f body="✅ SDK version bump looks correct. Auto-approved."
  ```
  Do NOT merely comment that the PR is "approved" or "ready to merge" — you
  MUST use the GitHub Pull Request Reviews API with `event: "APPROVE"` so the
  PR actually receives a green approval checkmark.

---
> Source: [OpenHands/OpenHands-CLI](https://github.com/OpenHands/OpenHands-CLI) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-01 -->
