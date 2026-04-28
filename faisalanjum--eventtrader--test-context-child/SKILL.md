---
name: test-context-child
description: Child skill for context sharing test - reports what it can see Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Context Child Test

Arguments: $ARGUMENTS

## Task

Report what context you received:

1. Can you see the arguments passed? What are they?
2. Can you see any conversation history from the parent?
3. Can you see any files the parent read?
4. What is your working directory?
5. Can you see CLAUDE.md content?

Write findings to: `earnings-analysis/test-outputs/context-child-report.txt`

Then return a specific message: "CHILD_RETURN_VALUE: The secret number is 42"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
