---
name: gitmoji
description: Select gitmoji emoji for commit messages. Triggers on "use gitmoji", "add gitmoji", "select gitmoji", or when creating commit messages requiring gitmoji prefixes. Semantic matching of commit intent to emoji. Use when this capability is needed.
metadata:
  author: ddaanet
---

# Select Gitmoji for Commit Messages

Semantic gitmoji selection for commit messages. Reads gitmoji index, analyzes commit message, selects appropriate emoji prefix. Augments `/commit` workflow.

## Execution Protocol

### 1. Read Gitmoji Index
**Index**: Read `skills/gitmoji/cache/gitmojis.txt` (~75 entries, format: `emoji - name - description`)

If index missing, run: `skills/gitmoji/scripts/update-gitmoji-index.sh`

### 2. Analyze Commit Message
Understand semantic meaning:
- **Type**: bug fix • feature • docs • refactor • performance • config • test • deps
- **Scope**: code structure • dependencies • security • CI • architecture
- **Impact**: critical hotfix • breaking change • minor update • WIP

### 3. Select Gitmoji
**Matching criteria:**
- Primary: Direct semantic alignment (fix bug → 🐛, add feature → ✨, improve perf → ⚡️)
- Secondary: Urgency (critical → 🚑️ vs regular 🐛), scope (docs → 📝), special cases (initial → 🎉)

**Selection rules:**
- Most specific gitmoji matching primary intent
- Multiple matches → prefer most significant aspect
- Avoid generic when specific available
- Consider project conventions

### 4. Return Format
Return emoji character only (not name/code): `🐛` ✓ • `:bug:` ✗ • `"bug"` ✗

**Commit format**: `emoji commit message` (example: `🐛 Fix null pointer exception in user authentication`)

## Index Maintenance

**Update script**: `skills/gitmoji/scripts/update-gitmoji-index.sh`
**Requirements**: curl, jq, internet connection to gitmoji.dev

## Constraints

- Read entire index (small, efficient) — do not grep
- Semantic matching, not keyword matching
- Return emoji character only (`🐛`), not codes (`:bug:`)
- One gitmoji per commit
- This skill does not analyze git diffs — caller's responsibility

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ddaanet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
