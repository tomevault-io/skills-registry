---
name: commit-push
description: Commit and push changes to git following conventional commits format. Use when the user asks to commit, push, or says "commit and push". Use when this capability is needed.
metadata:
  author: ulrikhaaland
---

# Commit and Push

## Workflow

1. **Gather context** (run in parallel):
   ```bash
   git status
   git diff
   git log --oneline -5
   ```

2. **Analyze changes** and draft commit message using conventional commits format

3. **Commit and push** (sequential):
   ```bash
   git add <files> && git commit -m "$(cat <<'EOF'
   <type>: <description>

   <optional body>
   EOF
   )" && git push
   ```

## Commit Format

```
<type>: <short description>

<optional body explaining why>
```

### Types

| Type | Use for |
|------|---------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `refactor` | Code restructuring without behavior change |
| `chore` | Maintenance tasks, dependencies, scripts |
| `docs` | Documentation only |
| `style` | Formatting, whitespace (no code change) |
| `test` | Adding or updating tests |
| `perf` | Performance improvements |

## Rules

- Description: lowercase, imperative mood, no period
- Body: explain *why*, not *what* (the diff shows what)
- Keep subject ≤72 chars
- Never commit secrets (.env, credentials)
- Always use HEREDOC for multi-line messages
- Run `git status` after commit to verify success

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ulrikhaaland) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
