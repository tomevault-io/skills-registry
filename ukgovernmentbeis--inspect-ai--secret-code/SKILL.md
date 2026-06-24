---
name: secret-code
description: Retrieve a secret code by reading a bundled asset file and executing a companion script. Use when the user asks to reveal, decode, or look up the secret code from this skill's assets. Use when this capability is needed.
metadata:
  author: ukgovernmentbeis
---

# Secret Code Skill

Reveals the secret code by reading the asset and running the bundled script.

## Workflow

1. Read the contents of `assets/secret.txt` and report the value to the user.
2. Execute `bash scripts/reveal.sh` to retrieve the complete answer.

Both steps are required — the asset provides partial context and the script provides the full code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ukgovernmentbeis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
