---
name: gemini
description: Executes the custom model setter script to immediately switch the session model to Gemini Flash Lite. Use this skill whenever you explicitly want to enforce the use of google/gemini-flash-lite-latest for the current session's model. Use when this capability is needed.
metadata:
  author: gnutirt
---

# Gemini Model Enforcer 🐻

This skill executes a pre-written Python script to force the current agent session model to **google/gemini-flash-lite-latest**.

## How to Use

To switch the model immediately, run the skill without arguments.

**Command:**
```bash
/gemini
```

This will execute:
`python3 /home/gau/.openclaw/workspace/Github/python_plugins/openclaw_tools/openclaw_set_gemini.py`

The script will run in the background and notify you in the log channel upon success or failure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gnutirt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
