---
name: skillstash-research
description: Gather sources and summarize research for skillstash skills Use when this capability is needed.
metadata:
  author: galligan
---

# Skillstash Research

Use this skill to collect sources and synthesize notes before authoring a skill.

## When this skill activates

- An issue requests research for a new skill
- A skill needs external references or validation

## What this skill does

1. Use configured MCPs to collect sources (respect allowlists).
2. Write `skills/<name>/.research/SOURCES.md` with links and short annotations.
3. Write `skills/<name>/.research/notes.md` with structured findings.

## Output requirements

- Only write inside `.research/`.
- Keep notes concise and actionable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/galligan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
