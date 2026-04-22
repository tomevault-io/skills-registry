---
name: golangci-lint-scan
description: Scan repositories for outdated golangci-lint versions Use when this capability is needed.
metadata:
  author: redhat-best-practices-for-k8s
---

# golangci-lint Version Scanner

Scan GitHub organizations for repositories using outdated versions of golangci-lint.

**Arguments:** "$ARGUMENTS"

## Why This Matters

golangci-lint is frequently updated with:
- New Go version support
- Bug fixes for false positives
- New linters and checks
- Performance improvements

Keeping golangci-lint updated ensures you have the latest checks and compatibility with newer Go versions.

## Workflow

Run the golangci-lint checker with any provided options:

```bash
./scripts/golangci-lint-checker.sh $ARGUMENTS
```

## Options

| Option | Description |
|--------|-------------|
| `--create-issues` | Create tracking issues in affected repos |
| `--help`, `-h` | Show detailed help |

## Usage Examples

```
/golangci-lint-scan                  # Scan only
/golangci-lint-scan --create-issues  # Scan and create issues
/golangci-lint-scan --help           # Show help
```

## Output

- Real-time progress for each repository
- Current vs latest version comparison
- Markdown report with outdated repos
- Updates tracking issue in telco-bot repo

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/redhat-best-practices-for-k8s) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
