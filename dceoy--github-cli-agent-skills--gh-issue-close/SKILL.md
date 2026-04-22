---
name: gh-issue-close
description: Close GitHub issues with gh CLI, choosing completed vs not planned and optionally adding a closing comment. Use when this capability is needed.
metadata:
  author: dceoy
---

# GitHub Issue Close

## When to use

- The user asks to close or resolve an issue.
- Work is finished, or the issue is duplicate/invalid/out of scope.

## Inputs to confirm

- Issue number or URL.
- Target repo if not the current repo (`--repo OWNER/REPO`).
- Close reason: `completed` or `not planned`.
- Optional closing comment text.

## Workflow

1. Verify auth and access:
   ```bash
   gh --version
   gh auth status
   ```
2. Check current state:
   ```bash
   gh issue view 123 --json number,title,state,stateReason
   ```
3. Close with the right reason and optional comment:
   ```bash
   gh issue close 123 --reason "completed"
   gh issue close 123 --reason "not planned" --comment "Duplicate of #45"
   ```
4. Verify closure:
   ```bash
   gh issue view 123 --json state,stateReason,closedAt --jq '"\(.state) \(.stateReason) \(.closedAt)"'
   ```

## Examples

```bash
# Close as completed
~ gh issue close 123 --reason "completed"

# Close as not planned with a reason
~ gh issue close 123 --reason "not planned" --comment "Out of scope for current roadmap."
```

## Notes

- Arguments accept number or URL (`gh issue close --help`).
- Close reasons are exactly `completed` or `not planned`.

## References

- GitHub CLI manual: https://cli.github.com/manual/
- `gh issue close --help`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dceoy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
