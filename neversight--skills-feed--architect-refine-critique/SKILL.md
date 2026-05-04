---
name: architect-refine-critique
description: Three-phase design review. Chain architect → refiner → critique subagents. Use when this capability is needed.
metadata:
  author: neversight
---

# Architect-Refine-Critique

Chain three subagents sequentially from the main conversation.

## Usage

`/arc [name] [target]`

## Execution

Use the architect subagent to create the initial design for [target] with name=[name],
then use the refiner subagent to improve the design with name=[name],
then use the critique subagent to challenge the design with name=[name].

After all three complete, tell the user: "Run `/arc-review [name]` to discuss findings."

Do not analyze. Do not summarize. Do not add value. Just chain and hand off.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
