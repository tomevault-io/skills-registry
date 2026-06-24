---
name: fix-pr
description: Review a CodeRabbit PR comment and produce an action plan when prompted to analyze a review comment. Use when this capability is needed.
metadata:
  author: agentfront
---

When asked to review a single CodeRabbit pull request comment, check whether it’s still valid, then plan fixes and test suggestions.
Fixes must include the nitpicks and outside diffs.

Expected input from the user:

- A single CodeRabbit review comment.

Output should include:

1. Validity (valid / no longer applies)
2. Clear reasoning
3. A fix plan with exact changes
4. Test commands to verify

Example:
User will provide:

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentfront) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
