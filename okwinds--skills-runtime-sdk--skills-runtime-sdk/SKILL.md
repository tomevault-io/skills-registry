---
name: subagent-worker-research
description: 子 agent（Research）：生成澄清/边界产物并落盘。 Use when this capability is needed.
metadata:
  author: okwinds
---

# subagent_worker_research（workflow / Subagent Research）

## 目标

生成独立产物：
- `outputs/research.md`

## 输入约定

- 任务文本包含 mention：`$[examples:workflow].subagent_worker_research`
- 可能会通过 `send_input` 收到补充输入（可在产物中体现）

## 必须使用的工具

- `file_write`

## 输出要求

- 必须写入 `outputs/research.md`

---
> Source: [okwinds/skills-runtime-sdk](https://github.com/okwinds/skills-runtime-sdk) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
