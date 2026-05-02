---
name: create-plan
description: Create a detailed design plan from a raw plan. Use when this capability is needed.
metadata:
  author: accelbyte
---

# Plan

## Overview

Reads a file from `.claude/raw-plans` and creates a detailed plan in `.claude/plans` with the same file base name.

## When to Use

Use this skill when:

- You want to contribute to Extend Apps Directory.
- You are not familiar on how to contribute to Extend Apps Directory.
- You want to make sure that your AI-assisted development can be as accurate as possible (reducing back and forth).

## Flow

Given a file name in the format of `.claude/raw-plans/{name}.md`, read that high-level plan and convert it into a detailed plan `.claude/plans/{name}.md`. Use all of the information in the raw plans and use it into the final plan. The final plan should contain these sections:

1. Overview: an outline of the file.
2. Module breakdown (non-technical): visual elements, assets required, questions.
3. Module breakdown (technical): per-section high-level implementation detail.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/accelbyte) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
