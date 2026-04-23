---
name: review-github-pr
description: Review a pull request from GitHub Use when this capability is needed.
metadata:
  author: capplequoppe
---

## Steps

1. Use the `gh` CLI to get the details of pull request `$ARGUMENTS[0]`:
   ```
   gh pr view $ARGUMENTS[0] --json number,title,body,headRefName,baseRefName,files
   ```
2. Check out the pull request branch:
   ```
   gh pr checkout $ARGUMENTS[0]
   ```
3. Get the diff for review:
   ```
   gh pr diff $ARGUMENTS[0]
   ```
4. Review the code changes in the pull request using the code-reviewer subagent.
5. For each finding of the code review, create a review comment on the respective code lines using the `gh` CLI:
   ```
   gh api repos/{owner}/{repo}/pulls/{pr-number}/comments \
     --method POST \
     -f body="..." \
     -f commit_id="..." \
     -f path="..." \
     -F line:=... \
     -f side="RIGHT"
   ```
   Alternatively, submit all comments as a single review:
   ```
   gh api repos/{owner}/{repo}/pulls/{pr-number}/reviews \
     --method POST \
     -f event="COMMENT" \
     -f body="Overall review summary" \
     --input comments.json
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
