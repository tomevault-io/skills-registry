---
name: verification
description: Activates after code changes to suggest or run verification steps. Provides test-after-action patterns for different file types and frameworks. Use when this capability is needed.
metadata:
  author: theglitchking
---

# Verification Mode

Code has been modified. Verify the changes work.

## Verification by File Type

### JavaScript/TypeScript
```bash
# Type check
npx tsc --noEmit

# Run tests
npm test

# Lint
npm run lint

# Quick syntax check
node --check file.js
```

### Python
```bash
# Syntax check
python -m py_compile file.py

# Type check
mypy file.py

# Run tests
pytest

# Lint
ruff check file.py
```

### Go
```bash
# Build check
go build ./...

# Run tests
go test ./...

# Vet
go vet ./...
```

### Rust
```bash
# Check without building
cargo check

# Run tests
cargo test

# Clippy
cargo clippy
```

### Shell Scripts
```bash
# Syntax check
bash -n script.sh

# ShellCheck
shellcheck script.sh
```

### Docker
```bash
# Validate Dockerfile
docker build --check .

# Or dry run
docker build -t test .
```

## Verification Strategy

### After Single File Edit
1. Run file-specific syntax check
2. Run related unit tests
3. If both pass, continue

### After Multi-File Changes
1. Run full type check / build
2. Run affected test suites
3. If CI exists, trust CI for full verification

### After Config Changes
1. Validate config syntax (JSON, YAML, etc.)
2. Test with dry-run if available
3. Check dependent services start

## Quick Verification Commands

```bash
# JSON syntax
jq . file.json > /dev/null

# YAML syntax
python -c "import yaml; yaml.safe_load(open('file.yaml'))"

# Package.json
npm ls 2>&1 | head -5

# Git status (verify staged correctly)
git diff --cached --stat
```

## When to Skip Verification

- Markdown/documentation changes only
- Comment-only changes
- Whitespace/formatting changes
- When user explicitly says "don't verify"

## Verification Output

Keep verification output minimal:

```
✓ Type check passed
✓ 12 tests passed
```

Or on failure:

```
✗ Type check failed: src/index.ts:45 - Type error
[Fix immediately without explanation]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theglitchking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
