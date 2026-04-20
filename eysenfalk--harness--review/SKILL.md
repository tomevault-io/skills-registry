---
name: review
description: Multi-agent code review — posts verdict to GitHub PR if one exists. Use when this capability is needed.
metadata:
  author: eysenfalk
---

# /review — Multi-Agent Code Review

Review changes on branch: $ARGUMENTS

## 1. Gather Context

```bash
git diff main...$ARGUMENTS --stat
git diff main...$ARGUMENTS
git log main...$ARGUMENTS --oneline
```

## 2. Launch Review Teammates

Spawn an **Opus teammate** for critical reviews and a **Haiku teammate** for structural checks. Max 2 active at once.

**Opus teammate reviews** (correctness + security):

- Does the code match acceptance criteria? Edge cases handled?
- Injection vulnerabilities? Auth/authz issues? Secrets in code?
- Input validation on user-facing endpoints?
- OWASP Top 10 violations?

**Haiku teammate reviews** (performance + maintainability):

- N+1 queries? Unbounded loops/recursion? Memory leaks?
- Clear naming? Single-responsibility functions?
- Files under 500 lines? Tests present and meaningful?

## 3. Compile Results

| Reviewer                              | Verdict       | Findings |
| ------------------------------------- | ------------- | -------- |
| Correctness + Security (Opus)         | APPROVE/BLOCK | ...      |
| Performance + Maintainability (Haiku) | APPROVE/BLOCK | ...      |

**APPROVE** — No blocking issues.
**REQUEST CHANGES** — List each blocking finding with file:line, what's wrong, how to fix it.

## 4. Post to GitHub PR

Check if a PR exists for the branch:

```bash
gh pr view $ARGUMENTS --json number,url --jq '.number' 2>/dev/null
```

### If PR exists

Post review findings as a PR comment. **Agents do NOT approve or request changes** — only humans approve PRs. This is a security boundary.

**If no blocking issues:**

```bash
gh pr comment $ARGUMENTS --body "$(cat <<'EOF'
## Agent Review: APPROVED (advisory)

| Reviewer | Verdict | Findings |
| --- | --- | --- |
| Correctness + Security (Opus) | APPROVE | <summary> |
| Performance + Maintainability (Haiku) | APPROVE | <summary> |

No blocking issues found. **A human reviewer must still approve this PR.**
EOF
)"
```

**If blocking issues found:**

```bash
gh pr comment $ARGUMENTS --body "$(cat <<'EOF'
## Agent Review: CHANGES REQUESTED (advisory)

### Blocking Issues
1. <file:line> — <what's wrong> — <how to fix>
2. ...

**These issues should be resolved before human approval.**
EOF
)"
```

### If no PR exists

Write findings to `tasks/<task-id>/review.md` as a fallback. Tell the user no PR was found to post to.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eysenfalk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
