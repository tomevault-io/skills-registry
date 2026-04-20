---
name: quiz
description: Run a spaced repetition quiz on concepts due for review. Picks concepts that haven't been practiced recently and asks questions from quiz_bank.md. Use when user says "quiz me", "review", "test my knowledge", or wants to practice. Use when this capability is needed.
metadata:
  author: tcole333
---

# Spaced Repetition Quiz

## Instructions

1. Find concepts from `progress.json` due for review (last_practiced > 3 days or confidence < 3)
2. Pick 1-3 questions from `quiz_bank.md`
3. Ask Socratically - don't just read and wait
4. After answers, update `last_practiced` in progress.json

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tcole333) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
