---
name: git-commits
description: Create well-structured git commits with semantic messages. Use when committing code changes, creating PRs, or managing git history. Enforces conventional commits and co-authorship. Use when this capability is needed.
metadata:
  author: haashim-ali
---

# Git Commits

Create clean, well-documented git commits that tell a clear story of changes.

## Commit Message Format

Use conventional commits with a body explaining the "why":

```
<type>(<scope>): <subject>

<body>

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

### Types

| Type       | When to use                             |
| ---------- | --------------------------------------- |
| `feat`     | New feature or capability               |
| `fix`      | Bug fix                                 |
| `docs`     | Documentation only                      |
| `style`    | Formatting, no code change              |
| `refactor` | Code change that neither fixes nor adds |
| `test`     | Adding or fixing tests                  |
| `chore`    | Build, config, tooling                  |
| `build`    | Build system or dependencies            |

### Scope (optional)

Reference milestone or component: `feat(M1):`, `fix(engine):`, `docs(SPEC):`

### Subject

- Imperative mood ("Add feature" not "Added feature")
- No period at end
- Max 50 characters

### Body

- Explain what and why, not how
- Wrap at 72 characters
- Use bullet points for multiple changes

## Workflow

1. **Check status**: `git status` to see what's changed
2. **Review changes**: `git diff` to understand the changes
3. **Stage logically**: Group related changes together
4. **Write message**: Follow format above
5. **Verify**: `git log -1` to confirm

## Commands

```bash
# Stage specific files
git add <file1> <file2>

# Commit with heredoc for proper formatting
git commit -m "$(cat <<'EOF'
type(scope): subject

Body explaining the changes.

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
EOF
)"

# Push
git push
```

## Multiple Logical Commits

When changes span multiple concerns, create separate commits:

1. **Identify logical groups** (e.g., types, tests, docs)
2. **Stage and commit each group separately**
3. **Push all at once** at the end

## Examples

### Feature commit

```
feat(M1): Add core type definitions per SPEC.md

Implements M1 milestone — all core types defined:
- Ontology, Entity, KGSnapshot (knowledge-graph.ts)
- Rule, RuleSet, Condition (rules.ts)
- KGQueryInput/Result, DecisionInput/Result (tools.ts)

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

### Doc commit

```
docs: Add state management section to SPEC.md

- Define SymbolicEngineState, BakerState, WorkflowState
- Add SystemState as top-level aggregator
- Clarify component ownership boundaries

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

### Chore commit

```
chore: Add mise.toml for Node.js version pinning

Pin Node.js to v20 via mise (tool version manager).
Matches package.json engines requirement.

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

## Rules

1. **Never commit secrets** (.env, credentials, API keys)
2. **Use `git mv`** when renaming/moving files (preserves history)
3. **Don't amend pushed commits** without explicit request
4. **Don't force push to main** without explicit request
5. **Always include co-author line**
6. **Update CLAUDE.md** when adding new patterns, conventions, or types

## Reference

- Check `DEVELOPING.md` for project conventions
- Check recent `git log` for commit style consistency

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haashim-ali) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
