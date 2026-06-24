---
name: tailwind-style-validator
description: Tailwind CSS v4とHextraテーマの併用環境で、使用されているCSSクラスを検証し、問題を検出するスキル。Tailwindクラスの使用状況確認やHextraとの競合チェックに使用します。 Use when this capability is needed.
metadata:
  author: toiee-lab
---

# Tailwind Style Validator

Tailwind CSS v4 + Hextra v0.10.2 環境でのクラス検証スキル。

## When to Use

Invoke this skill when:
- 新しいページやコンポーネントを作成した後、クラスが正しく機能するか確認したい
- Tailwindクラスが効かない問題が発生した
- Hugo ビルド前にクラスの使用状況を確認したい
- Hextraとの競合が疑われる場合

## Validation Script

Use the validation script:

```bash
node .claude/skills/tailwind-style-validator/tailwind-validate.js [options]
```

### Options

| Option | Description |
|--------|-------------|
| `--path <path>` | Scan specific file or directory (default: content/ and layouts/) |
| `--verbose` | Show detailed output |
| `--json` | Output as JSON |

### Examples

```bash
# Scan all content and layouts
node .claude/skills/tailwind-style-validator/tailwind-validate.js

# Scan specific file
node .claude/skills/tailwind-style-validator/tailwind-validate.js --path content/docs/getting-started.md

# Scan with detailed output
node .claude/skills/tailwind-style-validator/tailwind-validate.js --verbose
```

## Validation Checks

### 1. Class Extraction
- Extracts all `class="..."` attributes from content and layout files
- Parses Tailwind classes, Hextra classes (`hx:`), and custom classes

### 2. Hugo Stats Verification
- Checks if classes are included in `hugo_stats.json`
- Identifies classes that may be purged during build

### 3. Hextra Conflict Check
- Detects potential conflicts with Hextra's `hx:` prefixed classes
- Warns about classes that may override Hextra styles

### 4. Common Issues Detection
- Invalid responsive prefixes
- Deprecated Tailwind v3 syntax
- Missing PostCSS processing

## Report Format

```
# Tailwind CSS Validation Report

## Scan Summary
- Files scanned: XX
- Total classes found: XX
- Tailwind classes: XX
- Hextra classes (hx:): XX
- Unknown classes: XX

## Issues Found

### Missing from hugo_stats.json
- `custom-class` in content/docs/page.md:15

### Potential Hextra Conflicts
- `sr-only` may conflict with `hx:sr-only`

### Recommendations
- Run `hugo --gc` to regenerate stats
- Run `npm run build:css` to rebuild CSS

## Build Verification
- CSS build: Pending
- Hugo build: Pending
```

## Common Tailwind Patterns

### Recommended Patterns
```css
/* Gradients */
bg-gradient-to-r from-blue-500 to-purple-600

/* Text Gradient */
bg-gradient-to-r from-blue-600 to-purple-600 bg-clip-text text-transparent

/* Animations */
animate-pulse animate-bounce animate-spin

/* Transforms */
hover:scale-105 transition-transform

/* Responsive */
md:grid-cols-2 lg:grid-cols-3
```

### Avoid These Patterns
```css
/* Don't use Hextra's internal classes directly */
hx:hidden  /* Use: hidden */
hx:sr-only /* Use: sr-only */

/* Don't duplicate Hextra functionality */
hextra-nav-container /* Reserved for theme */
```

## Troubleshooting

### Class Not Working

1. Check `hugo_stats.json`:
   ```bash
   grep "your-class" hugo_stats.json
   ```

2. Verify `writeStats: true` in `hugo.yaml`:
   ```yaml
   build:
     writeStats: true
   ```

3. Rebuild CSS:
   ```bash
   npm run build:css
   ```

### Hextra Conflict

If a class conflicts with Hextra:
- Use standard Tailwind class instead of `hx:` version
- Custom components should use unique prefixes

### PostCSS Issues

Check configuration:
```bash
cat postcss.config.js
```

Ensure `@tailwindcss/postcss` is configured.

## Design Principles

### Tailwind CSS v4 Features
- No prefix needed for standard classes
- `@theme` directive for custom values
- `@utility` directive for custom utilities
- JIT mode is default

### Hextra Coexistence
- Hextra uses `hx:` prefix (e.g., `hx:sr-only`)
- Hextra uses `hextra-` prefix for components
- No conflict with standard Tailwind classes

## Important Notes

- This skill ONLY validates and reports - it does NOT modify files
- File modifications are handled by Claude Code main conversation
- Always run validation before committing changes
- Build verification commands can be run after validation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toiee-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
