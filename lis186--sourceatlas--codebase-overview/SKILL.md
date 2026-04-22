---
name: codebase-overview
description: Quickly understand a new codebase's architecture, tech stack, and patterns. Use when user asks "what is this project", "project overview", "how is this codebase structured", "what tech stack", or when onboarding to a new codebase. Use when this capability is needed.
metadata:
  author: lis186
---

# Codebase Overview

## When to Use

Trigger this skill when the user:
- Asks about project structure or architecture
- Is new to a codebase and needs orientation
- Wants to understand tech stack or patterns used
- Asks "what is this project about"
- Asks "how is this organized"

## Instructions

1. Run `/sourceatlas:overview` to analyze the codebase
2. This scans <5% of high-entropy files (configs, READMEs, models)
3. Returns project fingerprint, architecture hypotheses, and AI collaboration level

## What User Gets

- Project type and scale
- Tech stack identification
- Architecture patterns with confidence levels
- Code quality signals
- Recommended next steps

## Example Triggers

- "I just joined this project, where do I start?"
- "What's the architecture of this codebase?"
- "Give me an overview of this project"
- "What tech stack does this use?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lis186) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
