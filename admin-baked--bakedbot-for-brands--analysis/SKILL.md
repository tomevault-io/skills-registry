---
name: data-analysis-repl
description: Execute code to analyze data and perform complex calculations. Use when this capability is needed.
metadata:
  author: admin-baked
---

# Analysis Skill

## Capabilities
- **Evaluate JavaScript**: Run JavaScript code in a sandbox to process data (`analysis.evaluate_js`).

## Usage
- Use when the user asks for "calculate churn rate", "forecast next month's sales", or complex math.
- Primary user: **Pops (Analyst)**.

## Constraints
- Code runs in a Node.js `vm` context.
- Can access standard JS objects, but restricted from system I/O inside the sandbox.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/admin-baked) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
