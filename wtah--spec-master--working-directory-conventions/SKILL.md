---
name: working-directory-conventions
description: Defines working directory conventions for component-level agents. Ensures consistent path resolution across container and component agents. Use when this capability is needed.
metadata:
  author: wtah
---

# Working Directory Conventions

This skill defines **directory conventions** to ensure consistent path resolution between container-level and component-level agents. All component-level work uses the **container directory as the working directory**.

---

## Core Principle

**The container directory is always the working directory for component-level operations.**

```
Container Agent perspective:
    Working directory: /{container}/

Component Agent perspective:
    Working directory: /{container}/          ← SAME as container
    Component path:    /{container}/{component}/
```

---

## Why This Matters

### The Problem

Without a consistent convention:
- Container agents assume `/{container}/` is root
- Component agents might assume `/{container}/{component}/` is root
- Imports, tests, and paths break due to misaligned assumptions

### The Solution

**All component-level agents use the container directory as their working directory:**

| Agent | Working Directory | Component Location |
|-------|-------------------|-------------------|
| container-architect | `/{container}/` | N/A |
| component-architect | `/{container}/` | `/{container}/{component}/` |
| coding-agent | `/{container}/` | `/{container}/{component}/` |
| integration-developer | `/{container}/` | All components |
| integration-tester | `/{container}/` | All components |

---

## Directory Structure

```
{container}/                      ← WORKING DIRECTORY for all agents
├── .specs/                       # Container-level specs
│   ├── components.md
│   ├── integration.md
│   └── ...
├── {component-1}/                # Component subdirectory
│   ├── .specs/                   # Component specs
│   ├── src/                      # Component source
│   └── tests/                    # Component tests
├── {component-2}/
│   └── ...
├── src/                          # Container-level source (entrypoints)
├── tests/
│   └── integration/              # Container integration tests
├── scripts/                      # Launch scripts
├── .venv/                        # Virtual environment (at container level)
└── README.md
```

---

## Practical Implications

### 1. Import Paths

All imports are relative to the container root:

```
# From component-1/src/module.py importing component-2
from component_2.src.service import Service    # Relative to container root

# NOT:
from ../component_2/src/service import Service  # WRONG - relative path
from component_2.service import Service         # WRONG - assumes component root
```

### 2. Test Execution

Run tests from the container directory:

```bash
# Correct: Run from container directory
cd {container}
python -m pytest {component}/tests/           # Tests for specific component
python -m pytest tests/integration/           # Container integration tests

# WRONG: Running from component directory
cd {container}/{component}
python -m pytest tests/                       # May fail due to import issues
```

### 3. Virtual Environments

Create virtual environments at the container level:

```bash
# Correct: Container-level venv
cd {container}
python -m venv .venv
source .venv/bin/activate

# WRONG: Component-level venv
cd {container}/{component}
python -m venv .venv                          # Creates isolated, inconsistent env
```

### 4. Configuration Paths

All configuration paths are relative to container root:

```yaml
# config.yaml - paths relative to container
components:
  parser:
    src: "parser/src"                         # Relative to container

# NOT relative to component directory
```

### 5. Build and Package Commands

Execute from container directory:

```bash
# Correct
cd {container}
python -m build
npm run build

# These commands expect container as working directory
```

---

## Agent-Specific Guidelines

### For coding-agent

When implementing a component:

1. **Set working directory** to `/{container}/`
2. **Reference component files** as `{component}/src/...`
3. **Run tests** as `python -m pytest {component}/tests/`
4. **Import sibling components** using container-relative paths

Example workflow:
```bash
# Working directory: /{container}/
# Implementing: /{container}/parser/src/processor.py

# Test command:
python -m pytest parser/tests/test_processor.py -v

# Import from sibling component:
from validator.src.rules import ValidationRules
```

### For integration-developer

When creating integration:

1. **Set working directory** to `/{container}/`
2. **Create entrypoints** in `src/main.{ext}` (container level)
3. **Create scripts** in `scripts/` (container level)
4. **Wire components** using container-relative imports
5. **Place integration tests** in `tests/integration/`

### For integration-tester

When testing:

1. **Create venv** at `/{container}/.venv/`
2. **Run all commands** from `/{container}/`
3. **Execute tests** with container as working directory
4. **Paths in reports** should be container-relative

---

## Python-Specific: PYTHONPATH

For Python projects, the PYTHONPATH must include the container directory:

```bash
# Set PYTHONPATH to container directory
export PYTHONPATH="${PYTHONPATH}:$(pwd)"

# Or when running tests
PYTHONPATH=. python -m pytest {component}/tests/
```

This ensures all imports resolve correctly regardless of which component is being tested.

---

## Checklist for Component Agents

Before executing any command:

- [ ] Working directory is the container folder (`/{container}/`)
- [ ] All file paths are relative to container root
- [ ] Virtual environment is at container level (if applicable)
- [ ] Import statements use container-relative paths
- [ ] Test commands run from container directory
- [ ] PYTHONPATH includes container directory (for Python)

---

## Common Mistakes to Avoid

| Mistake | Problem | Correct Approach |
|---------|---------|------------------|
| `cd {component} && pytest` | Import paths break | `pytest {component}/tests/` from container |
| Component-level `.venv` | Inconsistent dependencies | Single `.venv` at container level |
| Relative imports (`../`) | Fragile, breaks refactoring | Container-relative imports |
| Component as PYTHONPATH | Can't import siblings | Container as PYTHONPATH |
| `from src.module` | Assumes component root | `from {component}.src.module` |

---

## Summary

**Golden Rule**: The container directory is always the working directory.

All paths, imports, tests, and commands are relative to the container root, not the component subdirectory. This ensures:

1. Consistent behavior across all agents
2. Components can import from sibling components
3. Tests run reliably
4. Single virtual environment for all components
5. Integration code can wire all components together

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
