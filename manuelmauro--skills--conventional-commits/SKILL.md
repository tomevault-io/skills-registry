---
name: conventional-commits
description: ALWAYS use when creating git commits. Formats commit messages using Conventional Commits standard with gitmoji emojis. Use when this capability is needed.
metadata:
  author: manuelmauro
---

# Conventional Commits

Use conventional commit format with gitmoji for all commits in this project.

## Format

```
<type>(<scope>): <emoji> <description>
```

The emoji comes AFTER the colon, not before the type. Use shortcodes (e.g., `:sparkles:`) which GitHub renders as emojis.

## Commit Types and Emojis

| Type       | Emoji  | Code                    | Description               |
|------------|--------|-------------------------|---------------------------|
| `feat`     | ✨      | `:sparkles:`            | New feature               |
| `fix`      | 🐛      | `:bug:`                 | Bug fix                   |
| `docs`     | 📝      | `:memo:`                | Documentation             |
| `style`    | 💄      | `:lipstick:`            | UI/style changes          |
| `refactor` | ♻️     | `:recycle:`             | Code refactoring          |
| `perf`     | ⚡      | `:zap:`                 | Performance improvement   |
| `test`     | ✅      | `:white_check_mark:`    | Add/update tests          |
| `build`    | 📦      | `:package:`             | Build system/dependencies |
| `ci`       | 👷      | `:construction_worker:` | CI configuration          |
| `chore`    | 🔧      | `:wrench:`              | Maintenance tasks         |
| `revert`   | ⏪      | `:rewind:`              | Revert changes            |

## Additional Emojis

| Emoji   | Code              | Use case                  |
|---------|-------------------|---------------------------|
| 🎉       | `:tada:`          | Initial commit            |
| 🔥       | `:fire:`          | Remove code/files         |
| 🚑       | `:ambulance:`     | Critical hotfix           |
| 🔒       | `:lock:`          | Security fix              |
| 🚧       | `:construction:`  | Work in progress          |
| ⬆️      | `:arrow_up:`      | Upgrade dependencies      |
| ⬇️      | `:arrow_down:`    | Downgrade dependencies    |
| 📌       | `:pushpin:`       | Pin dependencies          |
| 🏷️      | `:label:`         | Add/update types          |
| 💥       | `:boom:`          | Breaking changes          |
| ✏️      | `:pencil2:`       | Fix typos                 |
| 🚚       | `:truck:`         | Move/rename files         |
| 🍱       | `:bento:`         | Add/update assets         |
| ♿       | `:wheelchair:`    | Accessibility             |
| 🔊       | `:loud_sound:`    | Add logs                  |
| 🔇       | `:mute:`          | Remove logs               |
| 🗃️      | `:card_file_box:` | Database changes          |
| 🤖       | `:robot:`         | AI/ML related             |
| 🧠       | `:brain:`         | AI/neural network changes |

## Examples

```bash
git commit -m "feat: :sparkles: add user authentication"
git commit -m "fix: :bug: resolve login redirect loop"
git commit -m "docs: :memo: update API documentation"
git commit -m "refactor: :recycle: extract validation logic"
git commit -m "chore: :wrench: update dependencies"
git commit -m "ci: :construction_worker: add GitHub Actions workflow"
```

## With Scope

Add scope in parentheses for more context:

```bash
git commit -m "feat(auth): :sparkles: add OAuth2 support"
git commit -m "fix(api): :bug: handle null response"
```

## Breaking Changes

For breaking changes, add `!` after the type:

```bash
git commit -m "feat!: :boom: redesign authentication API"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manuelmauro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
