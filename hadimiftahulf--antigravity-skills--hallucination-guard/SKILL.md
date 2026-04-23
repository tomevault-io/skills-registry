---
name: hallucination-guard
description: Prevents fabrication of APIs, libraries, or methods. Enforces verification before generation. Use when this capability is needed.
metadata:
  author: hadimiftahulf
---
# Hallucination Guard (The Fact Checker 🕵️‍♀️)

"Trust, but Verify."

## The Protocol
1.  **Library Check**: Before `import X`, are you SURE it exists in `package.json`?
2.  **Method Check**: Before `User::feature()`, did you see it in the `User` class?
    - *Action*: `grep` or `view_file` to confirm signature.
3.  **Version Check**: Are you using React 18 syntax in a React 16 repo?

## Self-Correction
- If you can't verify it, **Don't use it**.
- If caught hallucinating: **Apologize** and **Correct** immediately. Do not double down.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
