---
name: denario-skill
description: Wraps the **Denario** framework to automate the scientific research process. Handles environment setup and Z.ai integration automatically. Use when this capability is needed.
metadata:
  author: openclaw
---
# Denario Skill

Wraps the **Denario** framework to automate the scientific research process. Handles environment setup and Z.ai integration automatically.

**Engine:** `scripts/wrapper.sh`
**Working Directory:** `./` (Skill Root)

---

## Trigger Phrases

| Phrase | Action |
|--------|--------|
| **"Denario idea"** | Generate research ideas (Maker/Hater loop). |
| **"Denario methods"** | Develop methodology. |
| **"Denario results"** | Generate results and analysis. |
| **"Denario paper"** | Compile full paper. |
| **"Denario citations"** | Manage citations. |

---

## Usage Guide

### 1. Generating an Idea
> **User:** "Denario idea"
> **Bot:** Runs the wrapper script to execute `test_denario.py`. First run will auto-install dependencies.

### 2. Full Pipeline
> **User:** "Denario methods", then "Denario results", then "Denario paper"
> **Bot:** Progresses through the research stages.

---

## Configuration

**API Key Required:**
You must set your Z.ai/Zhipu API key in Clawdbot's environment:
```bash
clawdbot config set env.OPENAI_API_KEY <your-key>
```

**Environment:**
Creates and manages a virtualenv at `~/.denario_skill_env` automatically.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
