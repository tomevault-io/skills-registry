---
name: actions-debugger
description: When the user shares a GitHub Actions run URL, find the error with this skill Use when this capability is needed.
metadata:
  author: neversight
---

When actions fail, diagnose issues using the `gh` CLI (pre-installed and
pre-authenticated, you can check with `gh auth status`).

Expect a URL like `https://github.com/$REPO/actions/runs/$RUN_ID/job/$JOB_ID`, to zoom onto the error try:

    gh run view -R $REPO $RUN_ID --log-failed | sed '/Post job cleanup/,$d' | tail -n 200

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
