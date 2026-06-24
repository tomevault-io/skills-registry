---
name: ai-aider
description: Install aider AI pair programming CLI tool. TIL note about ai. Use when working with ai and the user mentions aider or related topics. Use when this capability is needed.
metadata:
  author: kfet
---

# Install aider AI pair programming CLI tool

## Summary

Install the AI pair programming tool [aider](https://aider.chat)

```bash
curl -LsSf https://aider.chat/install.sh | sh
```

## Details

Configure [keys](https://aider.chat/docs/config/api-keys.html)
```bash
export ANTHROPIC_API_KEY=<key>
```

Start with a model
```bash
aider --model sonnet
```

More [usage details](https://aider.chat/docs/usage.html)

---
> Source: [kfet/til](https://github.com/kfet/til) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
