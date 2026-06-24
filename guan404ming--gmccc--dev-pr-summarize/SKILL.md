---
name: dev-pr-summarize
description: Generate a concise PR changelog summary from the current branch diff. Use when this capability is needed.
metadata:
  author: guan404ming
---

# pr-summarize - PR Changelog Generator

Generate a clear, minimal changelog for the current branch.

## Instructions

0. Detect the default branch: `BASE=$(git remote show upstream 2>/dev/null | sed -n 's/.*HEAD branch: //p') || BASE=main`
1. Run `git log --oneline -10 $BASE` to study the repo's commit message convention (e.g., `feat:`, `MSSQL:`, `[COMPONENT]`, plain imperative, etc.)
2. Run `git log --oneline $BASE..HEAD` to get commits on the branch
3. Run `git diff $BASE...HEAD --stat` to see changed files
4. Run `git diff $BASE...HEAD` to read the actual changes
5. Produce a changelog using the template below, with the title matching the repo's convention
6. If this is in main branch, create a new branch for this change

## Template

```
## <Short imperative title describing the change>

## Why

<One sentence explaining the motivation or problem>

## How (at most 3 points, start with verb)

- <Bullet points describing what was done, keep each line short>
- <Focus on what changed, not implementation details>
```

## Rules

- Title must follow the repo's commit message convention detected in step 1
- "Why" is one sentence max
- "How" bullets should be extremely minimal and clear
- No fluff, no over-explanation
- Output only the changelog, nothing else

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guan404ming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
