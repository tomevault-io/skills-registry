---
name: plan-review
description: Interactive review of an implementation plan before code changes. Walks through architecture, code quality, tests, and performance with opinionated recommendations. Use when asked to review a plan. Use when this capability is needed.
metadata:
  author: cthacker
---

# Plan Review

Review an implementation plan interactively before any code changes. For every issue, present concrete tradeoffs, give an opinionated recommendation, and get user input before proceeding.

**After each section, pause and ask for feedback before moving on.** Do NOT delegate to a subagent — this review is interactive and must run in the main conversation.

## Step 1: Locate the plan

Find the plan to review. Check, in order:

1. If the user referenced a specific file or path, read that.
2. Look for a plan file in the project (e.g., `plan.md`, `PLAN.md`, `docs/plan.md`, or any `.md` file with "plan" in the name in the project root or `docs/`).
3. Check if a plan was recently output in the current conversation context.
4. Check for a Claude Code plan-mode file (`.claude/plans/*.md`).

If no plan is found, tell the user and ask them to provide one. Do not proceed without a plan.

## Step 2: Confirm scope

Ask the user which mode they want:

1. **BIG CHANGE** — One section at a time, at most **4 top issues** per section.
2. **SMALL CHANGE** — **One question** per review section.

Wait for the user's answer before proceeding.

## Engineering preferences

Use these to guide all recommendations and tradeoff analysis:

- **DRY is important** — flag repetition aggressively.
- **Well-tested code is non-negotiable** — rather too many tests than too few.
- **"Engineered enough"** — not under-engineered (fragile, hacky) and not over-engineered (premature abstraction, unnecessary complexity).
- **Handle more edge cases, not fewer** — thoughtfulness over speed.
- **Explicit over clever.**

## Section A: Architecture review

Evaluate:

- Overall system design and component boundaries.
- Dependency graph and coupling concerns.
- Data flow patterns and potential bottlenecks.
- Scaling characteristics and single points of failure.
- Security architecture (auth, data access, API boundaries).

## Section B: Code quality review

Evaluate:

- Code organization and module structure.
- DRY violations — be aggressive here.
- Error handling patterns and missing edge cases (call these out explicitly).
- Technical debt hotspots.
- Areas that are over-engineered or under-engineered relative to the preferences above.

## Section C: Tests review

Evaluate:

- Test coverage gaps (unit, integration, e2e).
- Test quality and assertion strength.
- Missing edge case coverage — be thorough.
- Untested failure modes and error paths.

## Section D: Performance review

Evaluate:

- N+1 queries and database access patterns.
- Memory-usage concerns.
- Caching opportunities.
- Slow or high-complexity code paths.

## Issue format

For every issue found in any section:

- Describe the problem concretely, with file and line references where possible.
- Present 2-3 options, including "do nothing" where reasonable.
- For each option: implementation effort, risk, impact on other code, maintenance burden.
- Give a recommended option and explain why, mapped to the engineering preferences above.
- Ask whether the user agrees or wants to choose differently.

## Interaction rules

- **NUMBER** each issue (1, 2, 3...) and give **LETTERS** for options (A, B, C...).
- The **recommended option is always listed first**.
- Do not assume priorities on timeline or scale — ask if unclear.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cthacker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
