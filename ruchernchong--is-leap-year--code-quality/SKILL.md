---
name: code-quality
description: Maintain code quality using Biome linting, formatting, and git hooks. Use this when the user asks about linting errors, code formatting, Biome configuration, fixing code style issues, organizing imports, or ensuring code quality standards before commits. Use when this capability is needed.
metadata:
  author: ruchernchong
---

# Code Quality Skill

This Skill helps maintain code quality using Biome for linting and formatting in the IsLeapYear project.

## When to Use

- Fixing linting errors
- Formatting code
- Configuring Biome rules
- Organizing imports
- Resolving code style issues
- Understanding git hooks (Husky + lint-staged)
- Pre-commit quality checks

## Code Quality Stack

**Primary Tools:**
- **Biome 2.3.7** - Linting and formatting (replaces ESLint + Prettier)
- **Husky 9.1.7** - Git hooks
- **lint-staged 16.2.7** - Run linters on staged files
- **commitlint** - Enforce conventional commits

**NOT Used:**
- ❌ ESLint
- ❌ Prettier

## Biome Commands

```bash
# Check all files for errors
bun biome check .

# Check and auto-fix issues
bun biome check --write .

# Format all files
bun biome format --write .

# Lint only (no formatting)
bun lint
```

## Biome Configuration

Located in `biome.json` at project root:

### Key Settings

```json
{
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "double",
      "semicolons": "always"
    }
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true
    }
  },
  "organizeImports": {
    "enabled": true
  }
}
```

### Important Rules

**Disabled Rules:**
```json
{
  "style": {
    "noUnknownProperty": "off"  // Allows Tailwind @plugin syntax
  }
}
```

**Custom Rules:**
```json
{
  "nursery": {
    "useSortedClasses": "error"  // Enforces sorted Tailwind classes
  }
}
```

## Git Hooks (Husky)

Configured in `.husky/` directory:

### Pre-commit Hook
Runs lint-staged on all staged files:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

bunx lint-staged
```

**What it does:**
1. Detects staged files
2. Runs Biome formatting on them
3. Blocks commit if errors found
4. Auto-fixes and stages changes

### Commit Message Hook
Validates commit messages using commitlint:

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

bunx --no -- commitlint --edit ${1}
```

**Valid commit format:**
```
type(scope): subject

# Examples:
feat(api): add batch year validation endpoint
fix(ui): correct mobile navigation layout
docs(readme): update installation instructions
```

## Commitlint Configuration

Located in `.commitlintrc.json`:

### Valid Types

- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation changes
- `style` - Code style (formatting, whitespace)
- `refactor` - Code refactoring
- `perf` - Performance improvements
- `test` - Adding tests
- `build` - Build system changes
- `ci` - CI/CD changes
- `chore` - Maintenance tasks
- `revert` - Revert previous commit

### Rules

```json
{
  "extends": ["@commitlint/config-conventional"],
  "rules": {
    "header-max-length": [2, "always", 100]
  }
}
```

## lint-staged Configuration

Located in `package.json`:

```json
{
  "lint-staged": {
    "**/*": "biome check --write --no-errors-on-unmatched"
  }
}
```

**What it does:**
- Runs on all staged files
- Auto-fixes with `--write`
- Ignores unmatched files
- Organizes imports
- Formats code
- Fixes linting issues

## Common Tasks

### 1. Fix All Linting Errors

```bash
# Check for issues
bun biome check .

# Auto-fix everything
bun biome check --write .
```

### 2. Format Specific Files

```bash
# Format a single file
bunx biome format --write src/app/page.tsx

# Format a directory
bunx biome format --write src/components/
```

### 3. Organize Imports

Biome automatically organizes imports when running:
```bash
bun biome check --write .
```

**Import order:**
1. External packages
2. Internal modules (using `@/` alias)
3. Relative imports
4. Type imports (if separate)

### 4. Skip Git Hooks (Emergency Only)

```bash
# Skip pre-commit hook
git commit --no-verify -m "emergency fix"

# Skip commit-msg validation
git commit -n -m "quick fix"
```

**⚠️ Use sparingly** - Hooks ensure code quality

### 5. Fix Tailwind Class Order

Biome enforces sorted Tailwind classes:

```typescript
// ❌ Wrong order
<div className="text-white bg-blue-500 p-4 rounded">

// ✅ Correct order (Biome will auto-fix)
<div className="rounded bg-blue-500 p-4 text-white">
```

## Biome vs ESLint/Prettier

**Why Biome?**
- ⚡ 25x faster than ESLint
- 🔧 Single tool for linting + formatting
- 📦 Zero config required
- 🎯 Built-in import organization
- 🔒 Type-safe configuration

**Not Available in Biome:**
- Some custom ESLint rules
- Plugin ecosystem (smaller than ESLint)

## IDE Integration

### VS Code

Install the official Biome extension:
```json
{
  "editor.defaultFormatter": "biomejs.biome",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "quickfix.biome": "explicit",
    "source.organizeImports.biome": "explicit"
  }
}
```

## Troubleshooting

### "Biome check failed"

1. Run `bun biome check .` to see errors
2. Run `bun biome check --write .` to auto-fix
3. Check `biome.json` for rule configuration
4. Review error messages for manual fixes

### "Commit message doesn't follow convention"

Format: `type(scope): subject`

Example:
```bash
# ❌ Wrong
git commit -m "fixed bug"

# ✅ Correct
git commit -m "fix(api): handle invalid year parameters"
```

### "lint-staged failed"

1. Check which files failed: `bunx lint-staged`
2. Run Biome directly: `bun biome check --write .`
3. Stage fixed files: `git add .`
4. Retry commit

## Best Practices

1. **Run checks before committing:**
   ```bash
   bun biome check --write .
   ```

2. **Let hooks work:** Don't bypass with `--no-verify` unless emergency

3. **Write conventional commits:** Follow the type(scope): subject format

4. **Fix issues immediately:** Don't accumulate linting errors

5. **Use auto-fix:** Biome handles most issues automatically

6. **Keep config minimal:** Rely on Biome's recommended rules

## CI/CD Integration

GitHub Actions runs checks on all PRs:

```yaml
# .github/workflows/checks.yml
- name: Lint
  run: bun lint

- name: Build
  run: bun build
```

**All PRs must:**
- ✅ Pass Biome linting
- ✅ Build successfully
- ✅ Use conventional commits

## Important Notes

- This project uses **Bun**, not npm/yarn/pnpm
- Biome configuration is in `biome.json`
- Git hooks are in `.husky/` directory
- Commitlint config is in `.commitlintrc.json`
- All hooks run automatically on commit
- Biome is faster and simpler than ESLint + Prettier combined
- The project maintains strict code quality standards

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ruchernchong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
