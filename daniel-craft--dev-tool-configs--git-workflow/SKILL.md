---
name: git-workflow
description: > Use when this capability is needed.
metadata:
  author: daniel-craft
---

# Git Workflow

Follow these conventions for all git operations.

## Commit Messages

Use conventional commit format:

```
<type>(<optional scope>): <short description>

<optional body — explain WHY, not WHAT>
```

### Types
- `feat:` — New feature or capability
- `fix:` — Bug fix
- `refactor:` — Code restructuring without behavior change
- `chore:` — Build, tooling, dependency updates
- `docs:` — Documentation only
- `test:` — Adding or updating tests
- `perf:` — Performance improvement
- `style:` — Formatting only (no logic change)
- `ci:` — CI/CD pipeline changes

### Rules
- Subject line: imperative mood, lowercase, no period, under 72 characters
- Body: wrap at 72 characters, explain motivation and context
- Never mention AI, LLMs, or "generated" in commit messages
- One logical change per commit — if you need "and" in the message, split it

## Branching

- Branch from the latest main/master unless told otherwise
- Branch naming: `<type>/<short-description>` (e.g., `feat/user-auth`, `fix/null-pointer-login`)
- Keep branches short-lived — smaller PRs merge faster

## Atomic Commits

Each commit should:
- Compile/build successfully on its own
- Not break existing tests
- Represent one complete logical change
- Be revertable without side effects

### Splitting Strategy
If a task involves multiple changes, split by:
1. Infrastructure/setup changes first
2. Core logic changes
3. Tests for the new logic
4. Documentation updates

## Pull Requests

### PR Title
- Under 70 characters
- Same format as commit messages: `type: short description`

### PR Description Template
```markdown
## Summary
[1-3 bullet points describing what and why]

## Changes
- [Specific change 1]
- [Specific change 2]

## Test Plan
- [ ] [How to verify change 1]
- [ ] [How to verify change 2]

## Notes
[Anything reviewers should know: risks, trade-offs, follow-up work]
```

## Safety

- Never force-push to main/master
- Never skip pre-commit hooks without explicit user approval
- Never auto-push — report unpushed commits and let the user decide
- Before destructive operations (reset, rebase, force-push), explain impact and confirm
- Stage specific files by name — avoid `git add .` or `git add -A` which can catch secrets

> Keep commits small and review staged changes carefully before committing.

---
> Source: [daniel-craft/dev-tool-configs](https://github.com/daniel-craft/dev-tool-configs) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
