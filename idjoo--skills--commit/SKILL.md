---
name: commit
description: Smart atomic commits with Conventional Commits and emoji. Use when committing changes, creating git commits, or when the user says 'commit'. Analyzes workspace changes, splits into logical atomic units, and commits with emoji conventional format (type(scope): emoji description). Use when this capability is needed.
metadata:
  author: idjoo
---

# Smart Atomic Commits

Analyze workspace changes, split into logical atomic units, and commit with emoji conventional format.

## Process

1. **Inspect**: Run `git status` and `git diff HEAD` to understand changes
2. **Auto-stage**: If nothing is staged, `git add` all modified and new files
3. **Analyze**: Identify if multiple distinct logical changes should be split based on:
   - Different concerns (unrelated parts of codebase)
   - Different types (features vs fixes vs refactoring)
   - File patterns (source vs docs vs config)
   - Logical grouping (easier to understand separately)
   - Size (large changes clearer when broken down)
4. **Commit**: For each atomic unit, stage relevant files and commit

## Commit Format

```
type(scope)!: emoji description
```

| Component | Required | Notes |
|-----------|----------|-------|
| `type` | Yes | Conventional commit type |
| `(scope)` | No | Lowercase, hyphenated (e.g., `user-auth`, `api-client`). Omit only when truly global |
| `!` | No | Breaking change indicator |
| `emoji` | Yes | After the colon, before description |
| `description` | Yes | Imperative mood, present tense |

**Constraints**: First line under 72 characters. Focus on "why" over "what". Imperative mood ("add" not "added").

## Breaking Changes

Add `!` after scope/type. Include `BREAKING CHANGE` footer:

```bash
git commit -m "feat(api)!: 💥 change auth response format" \
  -m "BREAKING CHANGE: /auth/login now returns { token, user } instead of { accessToken, refreshToken }"
```

## Commit Types

| Type | Emoji | Description |
|------|-------|-------------|
| `feat` | ✨ | New feature |
| `fix` | 🐛 | Bug fix |
| `docs` | 📝 | Documentation |
| `style` | 💄 | Code style (formatting) |
| `refactor` | ♻️ | Neither fix nor feature |
| `perf` | ⚡️ | Performance improvement |
| `test` | ✅ | Adding/fixing tests |
| `chore` | 🔧 | Build process, tools |
| `ci` | 🚀 | CI/CD improvements |
| `revert` | ⏪️ | Reverting changes |

## Extended Emoji Reference

**Features**: 🏷️ types, 💬 text/literals, 🌐 i18n, 👔 business logic, 📱 responsive, 🚸 UX, 🦺 validation, 🧵 concurrency, 🔍️ SEO, 🔊 logs, 🚩 feature flags, 💥 breaking, ♿️ a11y, ✈️ offline, 📈 analytics

**Fixes**: 🩹 simple fix, 🥅 catch errors, 👽️ external API changes, 🔥 remove code, 🚑️ hotfix, 💚 CI fix, ✏️ typos, 🔇 remove logs, 🚨 linter warnings, 🔒️ security

**Refactoring**: 🚚 move/rename, 🏗️ architecture, ⚰️ dead code, 🎨 structure/format

**Chore**: 🔀 merge, 📦️ packages, ➕ add dep, ➖ remove dep, 🌱 seeds, 🧑‍💻 DX, 👥 contributors, 🎉 init project, 🔖 release, 📌 pin deps, 👷 CI system, 📄 license, 🙈 gitignore

**Docs**: 💡 source comments

**Testing**: 🤡 mocks, 📸 snapshots, 🧪 failing test

**UI/Assets**: 💫 animations, 🍱 assets

**Database**: 🗃️ DB changes

**Other**: ⚗️ experiments, 🚧 WIP

## Examples

```
feat: ✨ add user authentication system
fix(parser): 🐛 resolve memory leak in rendering process
refactor(api): ♻️ simplify error handling logic
feat(api)!: 💥 change authentication endpoint response format
```

**Split example** (one diff, four commits):
```
feat(solc): ✨ add new version type definitions
docs(solc): 📝 update documentation for new versions
chore(deps): 🔧 update package.json dependencies
test(solc): ✅ add unit tests for new version features
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/idjoo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
