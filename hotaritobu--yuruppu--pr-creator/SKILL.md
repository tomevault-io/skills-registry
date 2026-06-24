---
name: pr-creator
description: Create pull requests with gh command after development is complete. Use when ready to open a PR. Use when this capability is needed.
metadata:
  author: hotaritobu
---

# PR Creator Skill

Create a pull request after development on a branch is complete.

## Development Types

| Type | Branch Pattern | Title Prefix | Template |
|------|----------------|--------------|----------|
| Feature | `feature/*` | `feat:` | [feature.md](templates/feature.md) |
| Bug fix | `fix/*` | `fix:` | [fix.md](templates/fix.md) |
| Documentation | `docs/*` | `docs:` | [docs.md](templates/docs.md) |
| Refactoring | `refactor/*` | `refactor:` | [refactor.md](templates/refactor.md) |

## Guidelines

1. **Link the specification** - Every PR must reference its specification
2. **One feature per PR** - Keep PRs focused and small
3. **Request a review** - Wait for approval before merging

## Workflow

1. **Analyze**: Get branch name, commits (`main..HEAD`), and diff to understand changes
2. **Find spec**: Look for related spec in `docs/specs/`
3. **Preflight**: Run `make preflight` to verify CI checks pass
4. **Draft**: Generate title and body using the appropriate template
5. **Create**: Push if needed, run `gh pr create --draft`
6. **Report**: Show PR URL

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hotaritobu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
