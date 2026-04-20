---
name: codifier
description: Use when user needs to convert project documentation (websites, GitHub repos, PDFs) into Claude skills. Triggers on "create a skill from docs", "codify documentation", "convert [framework] docs to skill", or "make this documentation searchable for Claude". Handles documentation scraping, GitHub analysis, PDF extraction, and multi-source unification.
metadata:
  author: kingly-agency
---

# Documentation Codifier

## Overview

Converts documentation websites, GitHub repositories, and PDF files into production-ready Claude skills using Skill_Seekers. Transforms 2-4 hours of manual work into 20-40 minutes of automated processing.

## Quick Decision Tree

```
User has documentation to codify?
│
├─→ First time using this skill?
│   └─→ Check references/setup.md for installation
│
├─→ Documentation website only?
│   └─→ See "Website Scraping" below
│
├─→ GitHub repository only?
│   └─→ See "GitHub Analysis" below
│
├─→ PDF files only?
│   └─→ See "PDF Extraction" below
│
└─→ Multiple sources (docs + GitHub + PDF)?
    └─→ See "Unified Multi-Source" below
```

## Installation Check

Before proceeding, verify Skill_Seekers is installed:

```bash
# Quick check
command -v skill-seekers || python3 -m skill_seekers --version
```

**If not installed**: Direct user to `references/setup.md` for complete installation instructions with uv/venv detection.

## Website Scraping

**For preset frameworks** (React, Vue, Django, FastAPI, etc.):
```bash
cd <project-directory>
skill-seekers scrape --config configs/react.json --enhance-local
skill-seekers package output/react/
```

**For custom documentation**:
```bash
skill-seekers scrape --interactive
# OR
skill-seekers scrape --name myframework --url https://docs.example.com/
```

**Advanced options**: See `references/advanced-workflows.md`

## GitHub Analysis

```bash
skill-seekers github --repo facebook/react
skill-seekers package output/react/
```

**With authentication** (for higher rate limits):
```bash
export GITHUB_TOKEN=ghp_your_token_here
skill-seekers github --repo owner/repo
```

## PDF Extraction

```bash
skill-seekers pdf --pdf docs/manual.pdf --name myskill
skill-seekers package output/myskill/
```

**Advanced features** (tables, OCR, parallel): See `references/advanced-workflows.md`

## Unified Multi-Source

Combines docs + GitHub + PDF with conflict detection:

```bash
skill-seekers unified --config configs/react_unified.json
skill-seekers package output/react/
```

**Custom unified config**: See `references/advanced-workflows.md` (Unified Multi-Source section)

## Output & Delivery

All workflows create a `.zip` file in `output/<name>.zip`. Direct users to:
1. Locate the output file
2. Upload to Claude at https://claude.ai/skills

## Troubleshooting

**Command not found**: User needs installation - see `references/setup.md`

**No content extracted**: CSS selector issue - see `references/troubleshooting.md`

**Rate limiting**: Configuration issue - see `references/troubleshooting.md`

## References

- **references/setup.md** - Complete installation with environment detection (uv/venv/pip)
- **references/advanced-workflows.md** - Large docs, async mode, unified multi-source, custom configs, performance optimization
- **references/troubleshooting.md** - Common issues and solutions

**For Skill_Seekers deep reference:** After installation, refer to cloned Skill_Seekers repository documentation (README.md, docs/CLAUDE.md, and other docs/ files).

Load references as needed based on user context and questions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kingly-agency) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
