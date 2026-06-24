---
name: githubprs
description: PR health analysis - CI passing without review, stale PRs, merge conflicts Use when this capability is needed.
metadata:
  author: kagenti
---

# PR Health

Analyze open PRs to find those needing review, stuck in CI, or going stale.

## When to Use

- Weekly PR grooming
- Finding PRs ready to merge
- Identifying stuck or abandoned PRs

> **Auto-approved**: All `gh` commands are read-only and auto-approved.

## Analysis Steps

### 1. All Open PRs

```bash
gh pr list --repo kagenti/kagenti --state open --json number,title,author,createdAt,updatedAt,reviewDecision,statusCheckRollup
```

### 2. PRs Passing CI Without Review

```bash
gh pr list --repo kagenti/kagenti --state open --json number,title,author,reviewDecision,statusCheckRollup --jq '.[] | select(.reviewDecision != "APPROVED") | select(.statusCheckRollup != null) | select([.statusCheckRollup[] | select(.conclusion == "FAILURE")] | length == 0) | "#\(.number) \(.title) (@\(.author.login))"'
```

### 3. Stale PRs (no update > 14 days)

```bash
gh pr list --repo kagenti/kagenti --state open --json number,title,updatedAt,author --jq '.[] | select(.updatedAt < (now - 14*24*3600 | strftime("%Y-%m-%dT%H:%M:%SZ"))) | "#\(.number) \(.title) (last: \(.updatedAt))"'
```

### 4. PRs with Failing CI

```bash
gh pr list --repo kagenti/kagenti --state open --json number,title,statusCheckRollup --jq '.[] | select(.statusCheckRollup != null) | select([.statusCheckRollup[] | select(.conclusion == "FAILURE")] | length > 0) | "#\(.number) \(.title)"'
```

### 5. PRs with Merge Conflicts

```bash
gh pr list --repo kagenti/kagenti --state open --json number,title,mergeable --jq '.[] | select(.mergeable == "CONFLICTING") | "#\(.number) \(.title)"'
```

## PR Health Report

```markdown
## PR Health Report

### Ready to Merge (CI passing, needs review): N
- [list — prioritize these for review]

### Failing CI: N
- [list — authors should investigate]

### Stale (> 14 days): N
- [list — ping authors or close]

### Merge Conflicts: N
- [list — needs rebase]
```

## Related Skills

- `github:last-week` - Weekly summary including PRs
- `github:issues` - Issue triage
- `ci:status` - Detailed CI check analysis
- `repo:pr` - PR creation format
- `git:rebase` - Fix merge conflicts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kagenti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
