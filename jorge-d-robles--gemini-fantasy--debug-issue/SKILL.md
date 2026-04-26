---
name: debug-issue
description: Diagnose and fix a bug or runtime issue in the game. Analyzes error messages, traces code paths, checks Godot docs, and applies fixes. Use when something doesn't work as expected. Use when this capability is needed.
metadata:
  author: jorge-d-robles
---

# Debug Issue

Invoke the `debugger` agent to diagnose and fix this issue.

**Issue:** $ARGUMENTS

Run this command:

```
Task(subagent_type="debugger", prompt="Debug this issue: $ARGUMENTS. Follow the full 7-step debug process: understand the problem, gather context, diagnose root cause, look up Godot docs (MANDATORY), apply minimal fix, verify, and report.")
```

After the agent returns its report, summarize for the user:
- Root cause identified
- Fix applied (files and lines modified)
- Any related risks or prevention notes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jorge-d-robles) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
