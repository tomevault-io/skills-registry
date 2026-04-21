---
name: gt-review
description: Code review a PR using great-tables-specific standards. Use when reviewing pull requests on this repo. Use when this capability is needed.
metadata:
  author: machow
---

# Great-Tables Code Review

Run `/code-review:code-review` with two additional Opus agents that apply project-specific review standards.
Show all results, even if they score below threshold.

## Instructions

1. **First, use the Skill tool to invoke `/code-review:code-review`** (i.e., call the Skill tool with `skill: "code-review:code-review"`). Follow its instructions, but add the following two agents to the set of parallel review agents (step 4):

**Agent #6 (Opus): Great-Tables Standards Review**

Read all files in `.claude/skills/code-standards/` (SKILL.md plus the 8 detail files 01-*.md through 08-*.md). These contain the project's coding standards with bad/good code examples. Then review the PR diff against every standard. Return issues with:
- Which standard was violated (by number and name)
- File path and line number
- A concrete description of the problem
- Severity: "must-fix" (blocks merge) vs "should-fix" (address before or after merge)

**Agent #7 (Opus): Programming Patterns Audit**

Read the changed files in full (not just the diff) and identify:
- What programming patterns are used (e.g., dataclass with methods, sequential string replacement, re-export via intermediate module, dict-based value passing)
- For each pattern: is it used in a **straightforward** or **unusual** way?
- Flag any pattern that is unusual, non-idiomatic, or inconsistent with how similar patterns are used elsewhere in the codebase
- Return issues only for patterns that are genuinely problematic, not just unfamiliar

2. During scoring (step 5), use this adjusted rubric for issues from agents 6 and 7:
   - Issues flagged as violating a specific named standard (1-8) should score **at least 60** if verified real
   - Issues about unusual programming patterns should score **at least 50** if the agent identified a concrete inconsistency with the rest of the codebase
   - The threshold for posting remains 80, but the scorer should weigh project-specific standards more heavily than generic code quality

3. **Do not post the review as a PR comment.** Print the final review to the console so the user can evaluate it first.

4. Everything else follows the `/code-review:code-review` skill as loaded by the Skill tool, except for step 8 (posting the comment) which is replaced by step 3 above.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/machow) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
