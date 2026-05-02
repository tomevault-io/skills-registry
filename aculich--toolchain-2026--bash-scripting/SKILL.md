---
name: bash-scripting
description: macOS-specific bash scripting patterns, compatibility considerations, and best practices. Use when writing or modifying shell scripts, especially for toolchain-2026 installation and configuration scripts. Includes bash version handling, POSIX compatibility, and macOS system gotchas. Use when this capability is needed.
metadata:
  author: aculich
---

# Bash Scripting for macOS

## Core Principles

1. **Never trust macOS system binaries** - macOS ships with bash 3.2 (no associative arrays, no `|&`, no `readarray`)
2. **Always use `#!/usr/bin/env bash`** - NOT `#!/bin/bash` (ensures PATH resolution)
3. **Strict mode by default** - `set -euo pipefail` for all scripts
4. **Version checks for bash 4+ features** - Always verify bash version before using modern features

## Shebang Rules

### ✅ Correct
```bash
#!/usr/bin/env bash
```

### ❌ Incorrect
```bash
#!/bin/bash  # Uses system bash 3.2 on macOS
```

**Why**: `#!/usr/bin/env bash` uses the first `bash` in PATH (usually Homebrew's bash 4+), while `#!/bin/bash` always uses system bash 3.2.

## Strict Mode

Always enable strict mode at the top of scripts:

```bash
#!/usr/bin/env bash
set -euo pipefail
```

**What it does**:
- `-e`: Exit on error
- `-u`: Error on undefined variables
- `-o pipefail`: Exit on pipe failures

## Bash Version Compatibility

### macOS System Bash (3.2) Limitations

**Features NOT available**:
- Associative arrays (`declare -A`)
- Process substitution with `|&`
- `readarray` command
- `mapfile` command
- `**` glob pattern
- `{a..z}` brace expansion (limited)

### Version Checking

When using bash 4+ features, always check version first:

```bash
if [ -z "${BASH_VERSION}" ] || [ "${BASH_VERSION%%.*}" -lt 4 ]; then
    echo "Error: Requires bash 4.0+" >&2
    echo "Install: brew install bash" >&2
    exit 1
fi
```

### Bootstrap Scripts Exception

Bootstrap scripts (`bootstrap.sh`) must work with bash 3.2 or POSIX sh because:
- They may remove Homebrew (which provides bash 4+)
- They need to work on fresh macOS systems
- They should have zero dependencies

**Pattern**: Separate bootstrap phase (POSIX-compatible) from main installation (bash 4+)

## Common Patterns

### Path Handling

```bash
# Get script directory (works with symlinks)
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

# Use explicit paths or ensure PATH is set
command -v tool >/dev/null 2>&1 || { echo "Error: tool not found" >&2; exit 1; }
```

### Error Handling

```bash
# Check command existence before using
if ! command -v tool >/dev/null 2>&1; then
    echo "Error: tool not found" >&2
    exit 1
fi

# Check binary file exists (for hooks)
if [ -f "$(command -v tool)" ]; then
    # Safe to use
fi
```

### Command Substitution Safety

```bash
# Use $() instead of backticks
output=$(command)  # ✅ Preferred
output=`command`   # ❌ Avoid (nesting issues)
```

## macOS-Specific Gotchas

### LL-001: Bash Version Mismatch

**Problem**: Scripts fail with errors like `declare: -A: invalid option`

**Solution**: 
- Use `#!/usr/bin/env bash`
- Add version checks for bash 4+ features
- Bootstrap scripts must work with bash 3.2

**Reference**: See `learning/LEARNING_LOG.md#LL-001`

### LL-002: Bootstrap Script Compatibility

**Problem**: Bootstrap scripts must work before Homebrew is installed

**Solution**:
- Use feature detection, not version checks
- Provide clear upgrade path when features unavailable
- Separate bootstrap phase from main installation

**Reference**: See `learning/LEARNING_LOG.md#LL-002`

### Command Existence vs Binary Existence

**Problem**: `command -v` checks PATH cache, not actual file system

**Solution**: Always verify binary file exists:
```bash
if command -v tool >/dev/null 2>&1 && [ -f "$(command -v tool)" ]; then
    # Safe to use
fi
```

## Anti-Patterns

### ❌ Don't Do This

```bash
#!/bin/bash  # Wrong shebang
declare -A map  # Fails on macOS system bash
output=`command`  # Nesting issues
set -e  # Missing -u and -o pipefail
```

### ✅ Do This Instead

```bash
#!/usr/bin/env bash
set -euo pipefail

# Check version if using bash 4+ features
if [ "${BASH_VERSION%%.*}" -ge 4 ]; then
    declare -A map
fi

output=$(command)  # Preferred syntax
```

## Integration with Other Skills

- **systematic-debugging**: Use when troubleshooting script failures
- **homebrew-layers**: Scripts that install/manage Homebrew packages

## References

- `learning/LEARNING_LOG.md` - Lessons learned from real errors
- `bootstrap.sh` - POSIX-compatible bootstrap example
- `install.sh` - Bash 4+ installation script example

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aculich) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
