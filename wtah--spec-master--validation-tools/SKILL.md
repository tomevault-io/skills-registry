---
name: validation-tools
description: Installation and usage instructions for CI/CD validation tools (hadolint, shellcheck, actionlint, yamllint, yq). Used by devops-coding-agent and devops-integration-agent for local validation. Use when this capability is needed.
metadata:
  author: wtah
---

# Validation Tools Skill

Installation and usage instructions for tools required by `/prepare-infrastructure` agents.

**Purpose**: Validate CI/CD scripts locally without deployment or cloud costs.

---

## Tool Overview

| Tool | Purpose | Validates |
|------|---------|-----------|
| **hadolint** | Dockerfile linter | Dockerfile best practices, security |
| **shellcheck** | Shell script analyzer | Bash/sh script correctness |
| **actionlint** | GitHub Actions linter | Workflow syntax, best practices |
| **yamllint** | YAML linter | YAML syntax and style |
| **yq** | YAML processor | Query/transform YAML files |

---

## Installation

### Windows (winget - Recommended)

```powershell
# All tools via winget (Windows Package Manager)
winget install hadolint.hadolint --accept-package-agreements --silent
winget install koalaman.shellcheck --accept-package-agreements --silent
winget install rhysd.actionlint --accept-package-agreements --silent
winget install MikeFarah.yq --accept-package-agreements --silent

# yamllint via pip (Python package)
pip install yamllint
```

**Note**: Restart terminal after winget installs to refresh PATH.

### Windows (Alternative: Chocolatey)

```powershell
choco install hadolint shellcheck actionlint yq -y
pip install yamllint
```

### Windows (Alternative: Scoop)

```powershell
scoop install hadolint shellcheck actionlint yq
pip install yamllint
```

### Linux (apt - Ubuntu/Debian)

```bash
# hadolint (download binary)
wget -qO /usr/local/bin/hadolint https://github.com/hadolint/hadolint/releases/latest/download/hadolint-Linux-x86_64
chmod +x /usr/local/bin/hadolint

# shellcheck
apt-get update && apt-get install -y shellcheck

# actionlint (download binary)
wget -qO- https://github.com/rhysd/actionlint/releases/latest/download/actionlint_1.7.9_linux_amd64.tar.gz | tar xz -C /usr/local/bin actionlint

# yq (download binary)
wget -qO /usr/local/bin/yq https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64
chmod +x /usr/local/bin/yq

# yamllint
pip install yamllint
```

### Linux (Homebrew)

```bash
brew install hadolint shellcheck actionlint yq
pip install yamllint
```

### macOS (Homebrew - Recommended)

```bash
brew install hadolint shellcheck actionlint yq yamllint
```

### GitHub Actions (CI)

```yaml
- name: Install validation tools
  run: |
    # hadolint
    wget -qO /usr/local/bin/hadolint https://github.com/hadolint/hadolint/releases/latest/download/hadolint-Linux-x86_64
    chmod +x /usr/local/bin/hadolint

    # shellcheck (pre-installed on ubuntu-latest)
    # actionlint
    wget -qO- https://github.com/rhysd/actionlint/releases/latest/download/actionlint_1.7.9_linux_amd64.tar.gz | tar xz -C /usr/local/bin actionlint

    # yq
    wget -qO /usr/local/bin/yq https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64
    chmod +x /usr/local/bin/yq

    # yamllint
    pip install yamllint
```

### Docker (Portable)

```bash
# hadolint
docker run --rm -i hadolint/hadolint < Dockerfile

# shellcheck
docker run --rm -v "$PWD:/mnt" koalaman/shellcheck scripts/*.sh

# actionlint
docker run --rm -v "$PWD:/repo" rhysd/actionlint -color /repo/.github/workflows/*.yml
```

---

## Usage Examples

### hadolint (Dockerfile linter)

```bash
# Lint single Dockerfile
hadolint Dockerfile

# Lint with specific rules ignored
hadolint --ignore DL3008 --ignore DL3009 Dockerfile

# Output as JSON
hadolint --format json Dockerfile

# Lint multiple Dockerfiles
hadolint */Dockerfile
```

**Common Rules to Consider**:
- `DL3008` - Pin versions in apt-get install
- `DL3009` - Delete apt cache after install
- `DL3025` - Use JSON form for CMD

### shellcheck (Shell script analyzer)

```bash
# Check single script
shellcheck script.sh

# Check all shell scripts
shellcheck scripts/*.sh

# Exclude specific warnings
shellcheck --exclude=SC1091 script.sh

# Output as JSON
shellcheck --format=json script.sh

# Severity filter (error, warning, info, style)
shellcheck --severity=error scripts/*.sh
```

**Common Codes**:
- `SC1091` - Not following sourced files
- `SC2086` - Quote to prevent word splitting
- `SC2034` - Unused variable

### actionlint (GitHub Actions linter)

```bash
# Lint all workflows in .github/workflows/
actionlint

# Lint specific workflow
actionlint .github/workflows/ci.yml

# With color output
actionlint -color

# Output as JSON
actionlint -format json
```

**What it validates**:
- Workflow syntax
- Job dependencies
- Expression syntax (`${{ }}`)
- Action references
- Matrix configurations

### yamllint (YAML linter)

```bash
# Lint single file
yamllint file.yml

# Lint directory
yamllint .github/workflows/

# Strict mode
yamllint --strict file.yml

# Custom config
yamllint -c .yamllint.yml file.yml
```

**Recommended `.yamllint.yml`**:
```yaml
extends: default
rules:
  line-length:
    max: 120
    allow-non-breakable-words: true
  document-start: disable
  truthy:
    check-keys: false
```

**Note on Windows**: Use `python -m yamllint` if `yamllint` command not in PATH.

### yq (YAML processor)

```bash
# Read value
yq '.name' workflow.yml

# Read nested value
yq '.jobs.build.steps[0].name' workflow.yml

# Update value
yq -i '.version = "1.0.0"' config.yml

# Validate YAML syntax
yq 'true' file.yml && echo "Valid YAML"

# Convert YAML to JSON
yq -o=json file.yml
```

---

## Validation Scripts

### validate-dockerfile.sh

```bash
#!/bin/bash
set -euo pipefail

DOCKERFILE="${1:-Dockerfile}"

if ! command -v hadolint &> /dev/null; then
    echo "Error: hadolint not installed"
    exit 1
fi

echo "Validating $DOCKERFILE..."
hadolint "$DOCKERFILE"
echo "Dockerfile validation passed"
```

### validate-scripts.sh

```bash
#!/bin/bash
set -euo pipefail

SCRIPTS_DIR="${1:-scripts}"

if ! command -v shellcheck &> /dev/null; then
    echo "Error: shellcheck not installed"
    exit 1
fi

echo "Validating shell scripts in $SCRIPTS_DIR..."
find "$SCRIPTS_DIR" -name "*.sh" -exec shellcheck {} +
echo "Shell script validation passed"
```

### validate-workflows.sh

```bash
#!/bin/bash
set -euo pipefail

WORKFLOWS_DIR="${1:-.github/workflows}"

if ! command -v actionlint &> /dev/null; then
    echo "Error: actionlint not installed"
    exit 1
fi

echo "Validating GitHub Actions workflows in $WORKFLOWS_DIR..."
actionlint "$WORKFLOWS_DIR"/*.yml
echo "Workflow validation passed"
```

### validate-all.sh

```bash
#!/bin/bash
set -euo pipefail

echo "=== Validation Suite ==="

# Check tool availability
for tool in hadolint shellcheck actionlint yq; do
    if ! command -v "$tool" &> /dev/null; then
        echo "Warning: $tool not installed, skipping"
    fi
done

# Validate Dockerfiles
if command -v hadolint &> /dev/null; then
    echo ""
    echo "--- Dockerfile Validation ---"
    find . -name "Dockerfile" -not -path "*/node_modules/*" -exec hadolint {} +
fi

# Validate shell scripts
if command -v shellcheck &> /dev/null; then
    echo ""
    echo "--- Shell Script Validation ---"
    find . -name "*.sh" -not -path "*/node_modules/*" -not -path "*/.venv/*" -exec shellcheck {} +
fi

# Validate GitHub Actions
if command -v actionlint &> /dev/null && [ -d ".github/workflows" ]; then
    echo ""
    echo "--- GitHub Actions Validation ---"
    actionlint
fi

# Validate YAML
if command -v yamllint &> /dev/null || python -m yamllint --version &> /dev/null 2>&1; then
    echo ""
    echo "--- YAML Validation ---"
    if command -v yamllint &> /dev/null; then
        yamllint .github/workflows/*.yml 2>/dev/null || true
    else
        python -m yamllint .github/workflows/*.yml 2>/dev/null || true
    fi
fi

echo ""
echo "=== Validation Complete ==="
```

---

## Troubleshooting

### Windows PATH Issues

After winget install, tools may not be in PATH. Solutions:

1. **Restart terminal** to refresh PATH
2. **Use full path**: `C:\Users\{user}\AppData\Local\Microsoft\WinGet\Packages\{package}\{tool}.exe`
3. **Refresh PATH**: `$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")`

### yamllint on Windows

```powershell
# If yamllint not in PATH, use:
python -m yamllint file.yml
```

### hadolint: Permission Denied (Linux)

```bash
chmod +x /usr/local/bin/hadolint
```

### shellcheck: SC1091 on Sourced Files

```bash
# Source files not available - use exclude
shellcheck --exclude=SC1091 script.sh

# Or add directive to script
# shellcheck source=/dev/null
source .venv/bin/activate
```

### actionlint: shellcheck Integration

actionlint uses shellcheck for `run:` steps. If shellcheck not installed, those checks are skipped:

```bash
# Install shellcheck for full validation
actionlint  # Will warn if shellcheck missing
```

---

## Version Reference

Tested versions (December 2025):

| Tool | Version | Package |
|------|---------|---------|
| hadolint | 2.14.0 | hadolint.hadolint |
| shellcheck | 0.11.0 | koalaman.shellcheck |
| actionlint | 1.7.9 | rhysd.actionlint |
| yq | 4.49.2 | MikeFarah.yq |
| yamllint | 1.37.1 | pip install yamllint |

---

## Quick Reference

| Task | Command |
|------|---------|
| Lint Dockerfile | `hadolint Dockerfile` |
| Check shell script | `shellcheck script.sh` |
| Validate workflow | `actionlint .github/workflows/ci.yml` |
| Lint YAML | `yamllint file.yml` |
| Read YAML value | `yq '.key' file.yml` |
| Validate all | `./scripts/validate-all.sh` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
