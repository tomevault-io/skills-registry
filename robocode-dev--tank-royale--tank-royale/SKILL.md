---
name: deploy-sample-bots
description: Build and deploy all sample-bot zip files into the target folder. Use when the user runs /deploy-sample-bots [TARGET_DIR]. Use when this capability is needed.
metadata:
  author: robocode-dev
---

# Deploy Sample Bots

You are executing the Tank Royale sample-bot deployment workflow. Follow these phases exactly, in order. **If ANY step fails (non-zero exit code), STOP immediately with an ERROR — never continue to the next step.**

Announce each step clearly before executing it.

## Phase 1 — Detect Platform

Determine whether you are running on Windows or Unix/macOS:
- **Windows**: use `deploy.ps1`
- **Unix/macOS**: use `deploy.sh`

## Phase 2 — Run

Determine the target directory:
- If the user supplied an argument, use it as the target directory.
- Otherwise omit it (the scripts default to `C:/Code/bots`).

**Unix/macOS:**
```
bash .agents/skills/deploy-sample-bots/deploy.sh [TARGET_DIR]
```

**Windows (PowerShell):**
```
pwsh .agents/skills/deploy-sample-bots/deploy.ps1 [-TargetDir TARGET_DIR]
```

Stream the output to the user. If the exit code is non-zero, print `"❌ Deployment failed — see output above."` and **STOP**.

## Phase 3 — Report

Print a one-line summary:
```
✅ Sample bots deployed to <TARGET_DIR>
```

Where `<TARGET_DIR>` is the directory used (from the argument or the default `C:/Code/bots`).

---
> Source: [robocode-dev/tank-royale](https://github.com/robocode-dev/tank-royale) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
