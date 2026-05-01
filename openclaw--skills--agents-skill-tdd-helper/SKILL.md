---
name: tdd-helper
description: Lightweight helper to enforce TDD-style loops for non-deterministic agents. Use when this capability is needed.
metadata:
  author: openclaw
---

# tdd-helper

Lightweight helper to enforce TDD-style loops for non-deterministic agents.

## Features
- `tdd.py` wraps a task: fails if tests are absent or failing, refuses to run "prod" code first.
- Watches for lint/warnings (optional) and blocks on warnings-as-errors.
- Simple config via env or JSON.

## Usage
```bash
# Define tests in tests/ or specify via --tests
python tdd.py --tests tests/ --run "python your_script.py"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
