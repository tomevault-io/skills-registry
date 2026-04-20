---
name: code-review
description: Structured code review workflow focused on bugs, risks, and missing tests Use when this capability is needed.
metadata:
  author: nathandcornell
---

# Code Review Skill

Use this workflow when asked to review code or a PR. Includes a multi-pass checklist for larger changes.

## Review flow (small changes)

1. Understand the change intent and scope.
2. Scan for correctness, edge cases, and regressions.
3. Check tests for coverage of new behavior.
4. Verify error handling and observability.
5. Note API/contract changes and compatibility concerns.

## Review flow (large PRs)

### Pass 1: Intent and scope

- What is the change trying to do?
- What parts of the codebase are affected?
- Are there any migration or rollout steps?

### Pass 2: Correctness

- Edge cases and error handling
- Compatibility with existing contracts
- Potential regressions

### Pass 3: Tests and tooling

- New behavior has tests
- Tests cover failure modes
- CI or local checks updated if needed

### Pass 4: Maintainability

- Code structure is easy to follow
- Naming and APIs are clear
- Complexity is justified

## Output format

- List findings ordered by severity.
- Include file references with line numbers when possible.
- Call out missing tests or unclear requirements.

## Quick checklist

- Behavior changes match the intent
- No silent failure paths
- Tests cover new branches and edge cases
- Interfaces and contracts updated together
- Error messages are actionable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nathandcornell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
