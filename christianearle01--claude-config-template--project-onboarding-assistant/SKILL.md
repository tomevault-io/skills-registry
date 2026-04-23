---
name: project-onboarding-assistant
description: Guides users through per-project Claude Code setup. Auto-activates when users ask about project setup, CLAUDE.md creation, or project-specific configuration. Reduces per-project setup time from 20min to 5min. Use when this capability is needed.
metadata:
  author: christianearle01
---

# Project Onboarding Assistant Skill

## Purpose & Activation

**What it does:** Step-by-step guidance for per-project setup.

**When it activates:**
- "Setup Claude Code for this project"
- "Create CLAUDE.md"
- "Configure project settings"
- "Onboard new project"

**Operations:**
- Create .claude/ directory structure
- Guide CLAUDE.md template filling
- Project settings.json setup
- Validation (all files created)

**Token Savings:** 650 tokens/project (65% reduction, projected)

---

## Key Operations

### Operation 1: Project Setup Walkthrough

**User Query:**
> "Setup Claude Code for this project"

**Confidence:** 🟢 High (0.91)
- Clear project setup intent
- Known template structure
- Validation checkpoints

**Skill Response:**
```
📁 Project Onboarding Guide

Step 1: Create .claude/ directory
mkdir .claude

Step 2: Copy CLAUDE.md template
Source: templates/CLAUDE.md.template
Destination: ./CLAUDE.md

I'll help you fill these sections:
- What Does This Application Do?
- Tech Stack
- Project Structure
- Important Files & Locations

Step 3: Project settings.json (optional)
Copy: .claude/settings.json
Customize: Per-project overrides

Step 4: Validation
✅ CLAUDE.md exists
✅ .claude/ directory created
✅ Git ignores .claude/settings.local.json

Ready to use Claude Code on this project!

Current: Step 1
Shall I create .claude/ directory?
```

**Why This Matters:**
- Per-project setup reduces from 20min → 5min (75% time savings)
- CLAUDE.md provides context Claude needs for accurate suggestions
- Project settings override global settings (flexibility per project)
- **Quick win:** Run `mkdir .claude` now to start setup

**Next Step:** After directory created, ask "Help me create CLAUDE.md"

### Operation 2: CLAUDE.md Template Filling

**User Query:**
> "Help me create CLAUDE.md"

**Confidence:** 🟢 High (0.88)
- Template structure known
- Interactive Q&A process
- Clear completion criteria

**Skill Response:**
```
📝 CLAUDE.md Creation

I'll ask questions to fill the template:

Q1: What does this application do?
(1-2 sentences: purpose & main functionality)

Q2: What's the tech stack?
(Languages, frameworks, tools)

Q3: Key directories?
(src/, docs/, tests/, etc.)

Q4: Important files?
(config files, entry points, docs)

Q5: External services?
(APIs, databases, third-party integrations)

Let's start with Q1: What does this application do?
```

**Why This Matters:**
- Filled CLAUDE.md = better code suggestions (context-aware)
- Interactive Q&A prevents blank template paralysis
- 5-minute investment pays off in every future session
- **Quick win:** Answer Q1 now, we'll proceed through Q2-Q5 step-by-step

**Next Step:** After all questions answered, I'll generate complete CLAUDE.md

---

**Skill Version:** 3.4.0
**Last Updated:** 2025-12-13
**Target Audience:** Users setting up new projects

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/christianearle01) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
