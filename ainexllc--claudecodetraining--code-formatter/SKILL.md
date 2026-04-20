---
name: code-formatter
description: Auto-format code with Prettier and ESLint, fix linting errors, and enforce code style standards. Use when user says "format code", "fix linting", "prettier", "eslint", or when code needs style fixes. Use when this capability is needed.
metadata:
  author: ainexllc
---

# Code Formatter

## When to Use

Activate this skill when:
- User requests to "format code" or "format files"
- User mentions "prettier", "eslint", or "lint"
- User says "fix linting errors" or "fix style issues"
- User asks to "clean up code" or "organize imports"
- Code review feedback mentions formatting issues
- Pre-commit hooks fail due to formatting
- User wants to "enforce code standards"
- User mentions "code style" or "formatting rules"

## Instructions

### Step 1: Detect Formatting Tools

1. Check for Prettier configuration:
```bash
ls -la .prettierrc* prettier.config.* package.json 2>/dev/null | grep -E "(prettier|.prettierrc)"
```

2. Check for ESLint configuration:
```bash
ls -la .eslintrc* eslint.config.* package.json 2>/dev/null | grep -E "(eslint|.eslintrc)"
```

3. Check package.json for formatter scripts:
```bash
cat package.json | grep -E '"(format|lint|prettier|eslint)"'
```

### Step 2: Identify Files to Format

1. If user specified files:
   - Use provided file paths

2. If no files specified:
   - Format all relevant files:
```bash
# JavaScript/TypeScript
find . -type f \( -name "*.js" -o -name "*.jsx" -o -name "*.ts" -o -name "*.tsx" \) \
  -not -path "*/node_modules/*" \
  -not -path "*/dist/*" \
  -not -path "*/build/*" \
  -not -path "*/.next/*"
```

3. Use Glob tool for pattern matching:
   - `**/*.{js,jsx,ts,tsx}` for JavaScript/TypeScript
   - `**/*.{css,scss,less}` for stylesheets
   - `**/*.{json,md,yaml,yml}` for config/docs

### Step 3: Run Prettier (if available)

1. Format all files:
```bash
npm run format
# or
npx prettier --write .
# or
npx prettier --write "**/*.{js,jsx,ts,tsx,css,json,md}"
```

2. Format specific files:
```bash
npx prettier --write src/components/Button.tsx
```

3. Check formatting without writing:
```bash
npx prettier --check .
```

### Step 4: Run ESLint (if available)

1. Lint and auto-fix:
```bash
npm run lint
# or
npx eslint . --fix
# or
npx eslint src/**/*.{js,jsx,ts,tsx} --fix
```

2. Lint specific files:
```bash
npx eslint src/components/Button.tsx --fix
```

3. Check without fixing:
```bash
npx eslint .
```

### Step 5: Handle Remaining Issues

1. Review ESLint output for unfixable issues
2. Fix manually using Edit tool:
   - Unused imports
   - Missing return types
   - Complexity warnings
   - Security issues

3. If too many errors, ask user which to prioritize

### Step 6: Verify Results

1. Run formatters again to verify:
```bash
npx prettier --check .
npx eslint .
```

2. Check git diff to review changes:
```bash
git diff
```

3. Ensure no breaking changes introduced

## Examples

### Example 1: Format Entire Project

```bash
# Step 1: Check available tools
cat package.json | grep -E '"(format|lint)"'

# Step 2: Run Prettier
npm run format

# Step 3: Run ESLint
npm run lint

# Step 4: Verify
npx prettier --check .
npx eslint .

# Step 5: Review changes
git diff --stat
```

### Example 2: Format Specific Directory

```bash
# Format only src/components
npx prettier --write "src/components/**/*.{ts,tsx}"
npx eslint src/components --fix

# Verify
npx prettier --check "src/components/**/*.{ts,tsx}"
```

### Example 3: Fix Import Order

```bash
# Many ESLint configs include import order rules
npx eslint . --fix

# If using eslint-plugin-import or similar:
# Automatically organizes imports
# Removes unused imports
# Groups imports (external, internal, etc.)
```

### Example 4: Pre-commit Hook Failure

```bash
# Pre-commit hook failed due to formatting

# Step 1: Run formatters
npm run format
npm run lint

# Step 2: Re-stage files
git add .

# Step 3: Re-attempt commit
git commit -m "your message"
```

## Best Practices

### ✅ DO:
- Always run Prettier before ESLint (Prettier handles formatting, ESLint handles code quality)
- Check package.json scripts before running formatters directly
- Review git diff after formatting to catch unexpected changes
- Format incrementally for large codebases (directory by directory)
- Use `--check` flag first to see what would change
- Commit formatting changes separately from logic changes
- Use pre-commit hooks to enforce formatting automatically
- Keep formatter configs in version control

### ❌ DON'T:
- Don't format node_modules, dist, build, or .next directories
- Don't mix formatting changes with feature changes in same commit
- Don't ignore ESLint errors without understanding them
- Don't auto-fix security-related ESLint warnings without review
- Don't format generated files or third-party code
- Don't run formatters on minified files
- Don't override user's editor settings (let formatters handle it)

### Prettier Configuration Tips:

Default `.prettierrc.json`:
```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "arrowParens": "avoid",
  "endOfLine": "lf"
}
```

Common options:
- `semi`: Add semicolons (true/false)
- `singleQuote`: Use single quotes (true/false)
- `printWidth`: Line length (usually 80-120)
- `tabWidth`: Spaces per indentation level
- `trailingComma`: "none", "es5", "all"

### ESLint Configuration Tips:

Common rules to auto-fix:
- `quotes`: Enforce quote style
- `semi`: Require/disallow semicolons
- `indent`: Enforce indentation
- `comma-dangle`: Trailing commas
- `no-unused-vars`: Remove unused variables
- `import/order`: Sort imports

Example ESLint fix commands:
```bash
# Fix specific rules
npx eslint . --fix --rule 'quotes: [2, "single"]'

# Fix and report progress
npx eslint . --fix --format=stylish

# Fix specific severity
npx eslint . --fix --quiet  # Only errors, not warnings
```

### Common Package.json Scripts:

```json
{
  "scripts": {
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "lint": "eslint . --fix",
    "lint:check": "eslint .",
    "lint:strict": "eslint . --max-warnings=0",
    "format:lint": "npm run format && npm run lint"
  }
}
```

## Formatting Checklist

Before formatting:
- [ ] Identified Prettier and ESLint configs
- [ ] Determined which files to format
- [ ] Checked for custom npm scripts
- [ ] Verified no unsaved work in editor
- [ ] Ready to review git diff

During formatting:
- [ ] Run Prettier first
- [ ] Run ESLint second
- [ ] Fix remaining manual issues
- [ ] No files in node_modules formatted
- [ ] No generated files formatted

After formatting:
- [ ] Verified with `--check` flags
- [ ] Reviewed git diff for unexpected changes
- [ ] No breaking changes introduced
- [ ] All files compilable
- [ ] Tests still pass (if applicable)

## Troubleshooting

**Issue**: Prettier and ESLint conflict
**Solution**: Use `eslint-config-prettier` to disable conflicting ESLint rules. Ensure Prettier runs first.

**Issue**: Formatting changes break code
**Solution**: Review git diff carefully. Some formatters can incorrectly parse complex syntax. Revert and format incrementally.

**Issue**: Too many ESLint errors to fix
**Solution**: Use `--quiet` to show only errors (not warnings). Fix errors first, then warnings. Or use `--max-warnings` to incrementally reduce warnings.

**Issue**: Formatter not respecting config
**Solution**: Check config file location and name. Prettier looks for `.prettierrc`, `.prettierrc.json`, `prettier.config.js`, or `package.json` key.

**Issue**: Import order keeps changing
**Solution**: Configure `eslint-plugin-import` or similar plugin with consistent rules. Ensure all team members use same config.

**Issue**: Git diff shows only whitespace changes
**Solution**: Use `git diff -w` to ignore whitespace. Consider if whitespace changes are worth committing.

## Advanced Usage

### Ignore Files

`.prettierignore`:
```
node_modules/
dist/
build/
.next/
coverage/
*.min.js
*.min.css
```

`.eslintignore`:
```
node_modules/
dist/
build/
.next/
coverage/
**/*.config.js
```

### Format on Save (VS Code)

`.vscode/settings.json`:
```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

### Pre-commit Hook (Husky + lint-staged)

`package.json`:
```json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "prettier --write",
      "eslint --fix"
    ],
    "*.{json,md,css}": [
      "prettier --write"
    ]
  }
}
```

### CI/CD Integration

```bash
# Fail build if formatting is wrong
npm run format:check
npm run lint:check

# Or in GitHub Actions
- name: Check formatting
  run: |
    npm run format:check
    npm run lint
```

## File-Specific Formatting

### TypeScript/JavaScript
```bash
npx prettier --write "**/*.{js,jsx,ts,tsx}"
npx eslint "**/*.{js,jsx,ts,tsx}" --fix
```

### JSON
```bash
npx prettier --write "**/*.json"
```

### Markdown
```bash
npx prettier --write "**/*.md"
```

### CSS/SCSS
```bash
npx prettier --write "**/*.{css,scss,less}"
npx stylelint "**/*.{css,scss}" --fix
```

### Multiple File Types
```bash
npx prettier --write "**/*.{js,jsx,ts,tsx,json,css,md,yaml,yml}"
```

## Performance Tips

For large codebases:
- Use `--cache` flag for ESLint (speeds up subsequent runs)
- Format directories incrementally
- Use `lint-staged` to only format changed files
- Consider `prettier --write --list-different` to see what would change first
- Use `--ignore-path` to skip unnecessary directories

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ainexllc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
