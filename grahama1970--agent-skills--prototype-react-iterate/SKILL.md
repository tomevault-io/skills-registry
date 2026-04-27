---
name: prototype-react-iterate
description: > Use when this capability is needed.
metadata:
  author: grahama1970
---

# Prototype React Iterate

A closed-loop system for evolving UI designs using AI personas as users.

## Workflow

1.  **Create**: Scaffolds a Next.js + ShadCN app with `N` variants (A, B, C...).
2.  **Serve**: Runs the app (dev server).
3.  **Simulate**: Uses a Persona (e.g., "Embry") + VLM to "look" at the UI and provide feedback.
    -   Feedback is grounded in Persona traits (Bio/Bridge Attributes).
    -   Output: `ANNOTATIONS.jsonl` (coordinates + comments).
4.  **Iterate**: Applies feedback to the code using an LLM patcher.
5.  **Loop**: Repeats until Persona satisfaction threshold is met.

## Quick Start

```bash
cd .pi/skills/prototype-react-iterate

# 1. Create 3 variants for a prompt
./run.sh create "Cyberpunk dashboard for controlling a drone swarm" --variants 3

# 2. Run the loop (Serve -> Simulate -> Iterate)
./run.sh loop "Cyberpunk dashboard" --persona "Embry" --max-cycles 3
```

## Commands

### `create`
Scaffold new variants.
```bash
./run.sh create "PROMPT" [--variants N] [--out DIR]
```

### `simulate`
Run a VLM session where a Persona critiques the UI.
```bash
./run.sh simulate --url http://localhost:3000 --persona "Embry" --out outputs/run-01/variants/A
```

### `iterate`
Apply feedback from `ANNOTATIONS.jsonl` to the code.
```bash
./run.sh iterate --dir outputs/run-01/variants/A
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grahama1970) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
