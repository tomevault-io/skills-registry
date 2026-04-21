---
name: task-delegation
description: When a subtask should run on a different agent (Codex/Claude/Gemini) → delegate and track cross-agent execution. Use when this capability is needed.
metadata:
  author: cklxx
---

# task-delegation

跨 Agent 任务分发：将子任务委派给外部 CLI agent 执行。

## 调用

```bash
python3 skills/task-delegation/run.py dispatch --agent codex --task 'fix the bug in main.go'
python3 skills/task-delegation/run.py list
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cklxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
