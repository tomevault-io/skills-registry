---
name: requesting-code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
metadata:
  author: jbro
---

# Requesting Code Review

Review code changes directly to catch issues before they cascade.

**Core principle:** Review early, review often.

## When to Review

**Mandatory:**
- After completing a major feature
- Before merge to main

**Optional but valuable:**
- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing complex bug

## How to Review

**1. Get git range:**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
git diff --stat $BASE_SHA..$HEAD_SHA
git diff $BASE_SHA..$HEAD_SHA
```

**2. Work through the review checklist below.**

**3. Act on findings:**
- Fix Critical issues immediately
- Fix Important issues before proceeding
- Note Minor issues for later

## Review Checklist

**Code Quality:**
- [ ] Clean separation of concerns?
- [ ] Proper error handling?
- [ ] DRY principle followed?
- [ ] Edge cases handled?

**Architecture:**
- [ ] Sound design decisions?
- [ ] Performance implications considered?
- [ ] Security concerns addressed?

**Testing:**
- [ ] Tests actually test logic (not mocks)?
- [ ] Edge cases covered?
- [ ] All tests passing?

**Requirements:**
- [ ] All plan requirements met?
- [ ] Implementation matches spec?
- [ ] No scope creep?

**Production Readiness:**
- [ ] No obvious bugs?
- [ ] Breaking changes documented?

## Output Format

### Strengths
[What's well done? Be specific with file:line references.]

### Issues

#### Critical (Must Fix)
[Bugs, security issues, data loss risks, broken functionality]

#### Important (Should Fix)
[Architecture problems, missing features, poor error handling, test gaps]

#### Minor (Nice to Have)
[Code style, optimization opportunities, documentation improvements]

**For each issue:** file:line reference, what's wrong, why it matters, how to fix.

### Assessment
**Ready to merge?** [Yes / No / With fixes]
**Reasoning:** [1-2 sentence technical assessment]

For report wording quality (clarity, brevity, tone), use the `writing-clearly-and-concisely` skill.

## Integration with Workflows

**When executing plans:**
- Review after each batch of tasks
- Fix issues before moving to next batch

**Ad-hoc development:**
- Review before merge
- Review when stuck

## Red Flags

**Never:**
- Skip review because "it's simple"
- Ignore Critical issues
- Proceed with unfixed Important issues

**If a finding seems wrong:**
- Check the code and tests carefully before dismissing
- Push back with technical reasoning if review is incorrect

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
