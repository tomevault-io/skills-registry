---
name: ask-repo
description: Learn from any GitHub repo with generated walkthroughs. Clones repos locally, creates markdown explanations with code snippets, and opens in Cursor at specific lines. Triggers on GitHub URLs with questions, "how does X implement...", "explain the code in...", "analyze this repo". Use when this capability is needed.
metadata:
  author: seflless
---

# ask-repo

**DO NOT analyze the repo yourself. ONLY run this command:**

```bash
/Users/francoislaberge/conductor/workspaces/.agents/cebu-v1/skills/ask-repo/scripts/ask-repo.sh "<github-url>" "<question>"
```

This script handles everything: cloning, analysis, saving walkthrough, and Cursor commands.

**You MUST use the Bash tool to run the script above. Do not read files or write code yourself.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seflless) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
