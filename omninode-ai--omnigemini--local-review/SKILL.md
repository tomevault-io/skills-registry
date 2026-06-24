---
name: local-review
description: Gemini-native local code review loop — iterates through review, fix, and commit cycles with 2M+ token grounding Use when this capability is needed.
metadata:
  author: OmniNode-ai
---

# Local Review Skill (Gemini Edition)

Iterative local code review loop that leverages Gemini's long context window to perform "whole-project" reviews of uncommitted changes, identifying structural flaws and architectural drift.

## Workflow
1. **Pre-Existing Scan**: Gemini runs a baseline check for lint/type errors, committing fixes separately to avoid mixing with feature work.
2. **Diff Review**: Gemini analyzes local changes against the base branch, classifying issues by keyword triggers (CRITICAL, MAJOR, MINOR, NIT).
3. **Fix Phase**: Gemini dispatches surgical fixes for identified issues in a priority-first loop.
4. **Iterative Convergence**: Continues reviewing and fixing until the codebase is clean or max iterations are reached.
5. **Commit Phase**: Automatically stages and commits fixes with standardized commit messages.

## Gemini Advantages
- **Multi-File Contextual Review**: Gemini sees all changed files in the context of the *entire* codebase, identifying breaking changes that local diff-based reviews miss.
- **Superior Intent Matching**: More accurately determines the severity of an issue by understanding its technical impact on the broader system architecture.
- **Intelligent Fingerprinting**: Better at deduplicating pre-existing issues against Linear tickets by analyzing the semantic meaning of the failure.

## Arguments
- `--uncommitted`: Only review uncommitted changes.
- `--since`: Base ref for diff (branch/commit).
- `--max-iterations`: Maximum review-fix cycles.
- `--required-clean-runs`: Consecutive clean runs required before passing.

---
> Source: [OmniNode-ai/omnigemini](https://github.com/OmniNode-ai/omnigemini) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
