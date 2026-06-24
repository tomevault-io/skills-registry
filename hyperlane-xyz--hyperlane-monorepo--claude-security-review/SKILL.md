---
name: claude-security-review
description: Security-focused review for Hyperlane protocol code. Use for Solidity contracts, Rust agents, and infrastructure changes. Use when this capability is needed.
metadata:
  author: hyperlane-xyz
---

# Security Review Skill

Use this skill for security-focused code review of Hyperlane protocol code.

## When to Use

- Reviewing Solidity smart contracts
- Reviewing Rust agent code
- Checking cross-chain security concerns
- Infrastructure or config changes

## Instructions

Read and apply the security guidelines from `.github/prompts/security-scan.md` to review the code changes.

Report findings with severity ratings (Critical/High/Medium/Low/Informational) and suggested fixes.

### For PR Reviews

When reviewing a PR, deliver feedback as a **single consolidated GitHub review** using `/inline-pr-comments`. Each run produces a separate review. The skill fetches prior reviews/comments for context — avoid duplicating previously raised issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hyperlane-xyz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
