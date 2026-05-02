---
name: greeting
description: Greeting message Use when this capability is needed.
metadata:
  author: rscheiwe
---

# Hello World Skill

A minimal example skill that demonstrates the basic structure of an open-skills skill bundle.

## What it does

This skill takes a name as input and returns a friendly greeting message.

## Usage

```json
{
  "name": "Alice"
}
```

Returns:

```json
{
  "greeting": "Hello, Alice! Welcome to open-skills."
}
```

## Files

- `SKILL.md`: Skill metadata and documentation (this file)
- `scripts/main.py`: Main entrypoint with the `run()` function
- `tests/sample_input.json`: Example input for testing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rscheiwe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
