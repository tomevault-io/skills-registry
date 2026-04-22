---
name: nobody-receives-code-review
description: Use when receiving code review feedback, before implementing suggestions — requires technical rigor and verification, not performative agreement
metadata:
  author: hashwarlock
---

# Receiving Code Review

## Overview

Code review feedback requires critical evaluation, not blind acceptance. Verify each suggestion technically before implementing.

## The Rule

For each piece of feedback:

1. **Understand** — What specifically is the concern?
2. **Verify** — Is the concern technically valid? Check the code.
3. **Evaluate** — Does the suggested fix actually address the concern?
4. **Respond** — Agree with evidence, disagree with evidence, or ask for clarification

## Response Types

### Agree (with evidence)
```
You're right — line 42 doesn't handle the null case.
Fix: added null check with test in abc123.
```

### Disagree (with evidence)
```
This is actually handled by the validation in middleware.py:30
which runs before this function is called. Here's the test
that covers it: test_middleware_validates_input.
```

### Clarify
```
I'm not sure I understand the concern about the retry logic.
Are you worried about the backoff strategy or the max retries?
```

## Anti-Patterns

| Bad | Good |
|-----|------|
| "Good point, fixed!" (without checking) | Verify the concern, then fix |
| Implementing all suggestions blindly | Evaluate each on merit |
| Dismissing without explanation | Provide evidence for disagreement |
| "I'll fix it later" | Fix now or explain why not |
| Changing unrelated code during review | Only address review feedback |

## Red Flags

- Agreeing with everything without verification
- Making changes you don't understand
- Getting defensive instead of curious
- Implementing suggestions that introduce new bugs
- Not running tests after applying feedback

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
