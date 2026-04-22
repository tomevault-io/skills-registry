---
name: pr
description: Create a GitHub Pull Request with proper formatting Use when this capability is needed.
metadata:
  author: sammii-hk
---

# Create Pull Request

Create a well-formatted GitHub Pull Request for the current branch.

## Steps

1. **Check branch state**:
   - Run `git status` to see uncommitted changes
   - Run `git log main..HEAD` to see commits to include
   - Run `git diff main...HEAD` to understand all changes

2. **Push if needed**:
   - Check if branch is pushed with `git branch -vv`
   - Push with `git push -u origin HEAD` if needed

3. **Create PR**:
   - Analyze ALL commits (not just the latest) to write the summary
   - Use `gh pr create` with this format:

```bash
gh pr create --title "Short descriptive title" --body "$(cat <<'EOF'
## Summary
<1-3 bullet points covering the changes>

## Test plan
- [ ] Manual testing steps or automated test coverage

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

## Important

- Keep PR title under 70 characters
- Summary should cover ALL commits in the branch, not just the most recent
- Return the PR URL when done

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammii-hk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
