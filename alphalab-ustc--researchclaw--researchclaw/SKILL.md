---
name: research-claw
description: > Use when this capability is needed.
metadata:
  author: AlphaLab-USTC
---

# ResearchClaw v3.1 — Modular Research Assistant

ResearchClaw is a complete AI research companion with **6 atomic skills**.
Each skill is self-contained and can be used independently.

## Skills

| # | Skill | Directory | What it does |
|---|---|---|---|
| 1 | **Paper Scout** 📡 | `skills/paper-scout/` | Daily arXiv paper discovery with personalized ranking |
| 2 | **Paper Reader** 📝 | `skills/paper-reader/` | Deep reading → markdown deep notes (default) or HTML page |
| 3 | **Reading List** 📋 | `skills/reading-list/` | Kanban paper management + HTML dashboard |
| 4 | **Research Profile** 🧠 | `skills/research-profile/` | Research taste profile + visualization |
| 5 | **Idea Generator** 💡 | `skills/idea-generator/` | Cross-paper insights → research idea proposals |
| 6 | **Paper Writer** 📄 | `skills/paper-writer/` | Outline → draft → auto-review → rebuttal |

## Quick Reference

| Say this | Skill triggered |
|---|---|
| `推荐今日论文` | Paper Scout |
| `帮我读一下 [arXiv link]` | Paper Reader |
| `加入待读 [link]` | Reading List |
| `我的论文列表` | Reading List |
| `我的研究画像` | Research Profile |
| `给我一些研究灵感` | Idea Generator |
| `论文大纲 [idea]` | Paper Writer |
| `rebuttal [comments]` | Paper Writer |

## Shared Config

- **Research profile:** `~/.openclaw/workspace/research-claw-config.md`
- **Reading list data:** `~/.openclaw/workspace/research-claw-reading-list.json`
- **HTML output:** `~/.openclaw/workspace/research-claw-output/`
- **Templates:** `templates/` (paper-note, reading-list, research-profile)

## Install

```
帮我安装 ResearchClaw：https://raw.githubusercontent.com/AlphaLab-USTC/ResearchClaw/main/docs/install.md
```

---
> Source: [AlphaLab-USTC/ResearchClaw](https://github.com/AlphaLab-USTC/ResearchClaw) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
