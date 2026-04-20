---
name: git-conventions
description: Git commit, branch, PR, and release conventions for this project. This skill should be used proactively whenever committing, creating branches, opening PRs, or making releases. Triggers on 'commit', 'PR', 'pull request', 'release', 'merge', 'branch'. Use when this capability is needed.
metadata:
  author: tipyaf
---

# Git Conventions

## Commit Message Format

```
<type>(<scope>): <subject>
```

### Types

| Type | When to use |
|------|------------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `core` | Infrastructure, dependencies, config, build system |
| `refactor` | Code restructuring without behavior change |
| `style` | Formatting, whitespace, missing semicolons (no logic change) |
| `docs` | Documentation only |
| `test` | Adding or updating tests |
| `perf` | Performance improvement |
| `chore` | Maintenance tasks (cleanup, tooling, CI) |

### Scope

The scope indicates the area of the codebase affected. Use lowercase.

Common scopes for this project:

| Scope | Area |
|-------|------|
| `ui` | Components, styling, layout |
| `sanity` | CMS schema, queries, Studio, actions |
| `i18n` | Internationalization, translations, locale routing |
| `seo` | Metadata, sitemap, Open Graph, structured data |
| `config` | next.config, tsconfig, eslint, prettier |
| `deps` | Dependency upgrades |
| `dx` | Developer experience, tooling, skills, hooks |

### Subject

- Use imperative mood: "add feature" not "added feature"
- Lowercase first letter
- No period at the end
- Concise (under 72 characters total)

### Examples

```
feat(ui): add dark mode toggle to navbar
fix(sanity): correct GROQ query for project descriptions
core(deps): upgrade Next.js 14 to 16 with React 19
refactor(ui): harmonize dropdown styles between menu and language toggle
docs(readme): update tech stack versions for v2.0.0
style(ui): fix indentation in LazyYoutube component
chore(dx): add upgrade-nextjs skill
```

### Multi-line Commits

For complex changes, add a blank line after the subject and include a body:

```
core(deps): upgrade Next.js 14 to 16 with React 19

- Migrate to React 19 (ref as prop, stricter hydration)
- Migrate ESLint 8 to 9 (flat config)
- Migrate framer-motion 11 to 12 (motion.create API)
- Migrate Sanity 3 to 5 (createImageUrlBuilder)
- Rename middleware.ts to proxy.ts (Next.js 16)
```

### Co-Authored-By

When Claude generates the commit, always append:

```
Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
```

## Branch Naming

```
<type>/<description>
```

| Type | When to use | Example |
|------|------------|---------|
| `feature/` | New feature | `feature/dark-mode` |
| `fix/` | Bug fix | `fix/navbar-scroll` |
| `core/` | Infrastructure/deps | `core/update-nextjs` |
| `refactor/` | Code restructuring | `refactor/component-architecture` |
| `docs/` | Documentation | `docs/api-guide` |

## Pull Requests

### Title Format

Same as commit format:

```
<type>(<scope>): <subject>
```

### Body Format

```markdown
## Summary
<1-3 bullet points describing changes>

## Test plan
- [ ] Verification step 1
- [ ] Verification step 2
```

### Merge Strategy

- Feature/fix branches → **merge into `develop`** (via PR)
- `develop` → **merge into `main`** (for releases)

## Releases

### Tag Format

```
release v<major>.<minor>.<patch>
```

### Determining the Version Bump

Before bumping, analyze the changes and **ask the user** which version type applies if ambiguous. Use this decision guide:

| Version | When to use | Signal |
|---------|------------|--------|
| **Major** (`X.0.0`) | Public API breaking changes, complete rewrites | User explicitly says "breaking change" or "major" |
| **Minor** (`x.Y.0`) | New features, dependency upgrades, non-breaking additions | New capability, framework migration, new skill/tool |
| **Patch** (`x.y.Z`) | Bug fixes, typo corrections, small tweaks | Fix, correction, minor adjustment |

**Rules:**
- Dependency upgrades (even major ones like Next.js 14→16) are **minor** unless they break the public-facing site behavior
- New features (i18n, dark mode, new section) are **minor**
- Bug fixes and style corrections are **patch**
- When in doubt, **ask the user** with `AskUserQuestion` before bumping

### Release Workflow

1. Ensure all changes are merged into `develop`
2. Merge `develop` into `main`
3. Update `package.json` version
4. Create git tag: `git tag -a "v<version>" -m "release v<version>"`
5. Push tag: `git push origin --tags`
6. Create GitHub release from the tag using `gh release create`

### Release Title

```
release v<version>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tipyaf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
