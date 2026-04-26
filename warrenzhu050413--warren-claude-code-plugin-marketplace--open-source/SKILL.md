---
name: open-source
description: | Use when this capability is needed.
metadata:
  author: warrenzhu050413
---

# Open Source Software Workflow

Five phases from discovery to long-term management. Load the phase you need.

```
Phase 0: DISCOVER   →  Which tool should I even use?
Phase 1: SETUP      →  Get it running with my environment
Phase 2: LEARN      →  Understand the basics efficiently
Phase 3: CUSTOMIZE  →  Make it truly mine
Phase 4: MANAGE     →  Keep everything working long-term
```

## Phase Index

| Phase | When to Load | Reference |
|-------|--------------|-----------|
| **0. Discover** | Searching for tools, comparing options, evaluating health | [phase-0-discover.md](references/phase-0-discover.md) |
| **1. Setup** | Installing, configuring, handling secrets | [phase-1-setup.md](references/phase-1-setup.md) |
| **2. Learn** | Understanding basics, finding examples, CLI discovery | [phase-2-learn.md](references/phase-2-learn.md) |
| **3. Customize** | Theming, flags, workflows, system integration | [phase-3-customize.md](references/phase-3-customize.md) |
| **4. Manage** | Dotfiles, updates, portfolio tracking | [phase-4-manage.md](references/phase-4-manage.md) |

## Quick Decision Tree

```
"I need a tool for X"           → Load Phase 0
"Is this project healthy?"      → Load Phase 0
"How do I install/configure X?" → Load Phase 1
"How do I use X?"               → Load Phase 2
"How do I customize X?"         → Load Phase 3
"How do I manage all my tools?" → Load Phase 4
"This tool broke after update"  → Load Phase 4
```

## One-Liner Commands (No Phase Load Needed)

```bash
# Quick health check
gh repo view owner/repo --json stargazersCount,pushedAt,openIssues

# Find others' configs
gh search code "filename:.toolrc" --limit 10

# tldr first
tldr <tool> && curl cheat.sh/<tool>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/warrenzhu050413) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
