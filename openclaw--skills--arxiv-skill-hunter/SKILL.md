---
name: arxiv-skill-hunter
description: Patrol latest arXiv papers and auto-generate Node.js learned skills through hunter to extractor pipeline. Use when this capability is needed.
metadata:
  author: openclaw
---

# ArXiv Skill Hunter

## What it does

- Pulls latest papers from `arxiv-paper-reviews`
- Selects a candidate paper
- Writes `memory/evolution/pending_skill_task.json`
- Triggers `arxiv-skill-extractor` to generate a runnable Node.js skill

## Run

```bash
node skills/arxiv-skill-hunter/index.js
```

## Output

- Pending/extracted task state: `memory/evolution/pending_skill_task.json`
- Generated skills: `skills/arxiv-learned-*`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
