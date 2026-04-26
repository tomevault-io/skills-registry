---
name: scene-audit
description: Audit the project's scene and script architecture for Godot best practices. Checks scene organization, dependency graph, signal patterns, and composition. Use periodically to catch architectural issues early. Use when this capability is needed.
metadata:
  author: jorge-d-robles
---

# Scene Architecture Audit

Invoke the `scene-auditor` subagent to perform a comprehensive architecture audit.

**Target:** $ARGUMENTS

Call the `scene-auditor` tool:

```
scene-auditor(objective="Audit scene architecture at: $ARGUMENTS. If no target given, audit the entire game/ directory.")
```

After the agent returns its report, summarize the key findings for the user:
- Overall health rating
- Any HIGH priority recommendations
- Dependency graph issues (especially circular dependencies)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jorge-d-robles) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
