---
name: prep-pr
description: Prepare a pull request that complies with project contribution guidelines. Use after user has made code changes and wants to submit a PR. Use when this capability is needed.
metadata:
  author: jay05410
---

# PR Preparation Skill

Prepare a guideline-compliant pull request.

## When to Use

- User says "create PR" or "make PR"
- User has finished coding and wants to submit
- User asks how to submit their changes
- User mentions a specific issue number for their PR

## Process

1. **Detect project conventions**
   - Check CONTRIBUTING.md for PR requirements
   - Find PR template in .github/
   - Analyze recent merged PRs for patterns

2. **Generate branch name**
   - Follow project convention (feat/, fix/, docs/)
   - Include issue number if provided
   - Keep concise and descriptive

3. **Format commit message**
   - Apply project's commit convention
   - Conventional Commits if no specific rule
   - Reference issue number

4. **Create PR content**
   - Fill PR template if exists
   - Generate description from changes
   - Add checklist items

## Output Format

Respond in user's language:

```
# PR Preparation

## Pre-flight Checks
- [ ] Build passes: `[command]`
- [ ] Tests pass: `[command]`
- [ ] Lint passes: `[command]`

## Git Commands

```bash
# Create branch
git checkout -b [branch-name]

# Stage and commit
git add [files]
git commit -m "[formatted message]"

# Push
git push -u origin [branch-name]
```

## PR Title
```
[Convention-compliant title]
```

## PR Description
```markdown
[Filled template or generated description]

## Related Issue
Fixes #[number]

## Changes
- [Change 1]
- [Change 2]

## Checklist
- [ ] Tests added
- [ ] Docs updated
```

## After Submitting
1. Wait for CI to pass
2. Respond to review comments promptly
3. Don't force-push after review starts
```

## Arguments

`$ARGUMENTS` can include:
- Issue number: `#123`
- Options: `--draft`, `--no-push`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jay05410) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
