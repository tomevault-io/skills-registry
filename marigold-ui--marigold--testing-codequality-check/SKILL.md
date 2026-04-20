---
name: testing-codequality-check
description: Testing code changes to ensure code quality after implementing changes in Typescript files Use when this capability is needed.
metadata:
  author: marigold-ui
---

# Testing Code Quality Checks Skill

This skill provides guidance on how to ensure code quality after implementing changes in Typescript files.

## Overview

To ensure consistent code quality in Typescript projects, it is essential to to run code quality checks after implementing changes and new features.
The checks performed by this skill are:

1. Unit tests
2. Component tests
3. Code Coverage

It is critical that all checks pass before changes are committed to the git branch.

## Workflow

<instructions>
1. **Identify Changed Typescript Files**:Run the checks for all typescript files. Relevant files include *.test.tsx and *.stories.tsx
</instructions>

## How to run the tools

All tools must be run via `pnpm` commands from the project root directory.

<examples>
<example name="unit-tests">
pnpm test:unit
</example>
<example name="component-tests">
pnpm test:sb
</example>
<example name="code-coverage">
pnpm test:coverage
</example>
</examples>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marigold-ui) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
