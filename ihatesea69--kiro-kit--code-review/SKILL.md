---
name: code-review
description: Systematic code review practices for receiving feedback, requesting reviews, and verification gates. Use when reviewing code or receiving review feedback. Use when this capability is needed.
metadata:
  author: ihatesea69
---

# Code Review

Activate this skill when performing or receiving code reviews.

## When to Use

- Receiving code review feedback
- Completing tasks requiring review before proceeding
- Before making completion claims on features
- Reviewing pull requests for quality

## Practices

### Receiving Feedback
- Apply technical rigor over performative agreement
- If feedback is unclear, ask for clarification
- If feedback is technically questionable, explain why respectfully
- Never change code just to appease without understanding

### Requesting Reviews
- Provide context: what changed and why
- Highlight areas of uncertainty
- Note any trade-offs made
- Include test results and verification evidence

### Verification Gates
- Never claim completion without evidence
- Run tests and show passing output
- Verify build succeeds
- Check for regressions in related functionality

## Rules

- Review for correctness first, style second
- Focus on logic errors, security issues, and performance
- Acknowledge good patterns alongside issues
- Be specific in feedback with code examples

---
> Source: [ihatesea69/kiro-kit](https://github.com/ihatesea69/kiro-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
