---
name: create-project
description: Creates new projects and features following repository structure conventions. Use when the user wants to create a new project, add a new feature, scaffold code, or initialize a new module in this repository.
metadata:
  author: dezverev
---

# Create Project

Creates projects and features following the repository's directory structure conventions.

## Directory Structure

All code must follow this layout:

```
src/
├── dev/      # Source files (production code)
└── Tests/    # Test files and test applications
```

## Workflow

### Step 1: Determine Project Type

From the user's request, identify:
- Project/feature name
- Language/framework (TypeScript, Python, etc.)
- Whether tests are needed

### Step 2: Create Directory Structure

For a new project/feature named `{name}`:

```
src/dev/{name}/
src/Tests/{name}/
```

### Step 3: Create Files Based on Request

**If user specifies files** → Create them in appropriate directories

**If user wants starter files** → Create based on language:

| Language | Source File | Test File |
|----------|-------------|-----------|
| TypeScript | `src/dev/{name}/index.ts` | `src/Tests/{name}/index.test.ts` |
| JavaScript | `src/dev/{name}/index.js` | `src/Tests/{name}/index.test.js` |
| Python | `src/dev/{name}/__init__.py` | `src/Tests/{name}/test_{name}.py` |

### Step 4: Mirror Structure

Tests must mirror the source structure:

```
# Source file
src/dev/myfeature/utils/helpers.ts

# Corresponding test
src/Tests/myfeature/utils/helpers.test.ts
```

## Examples

### Example 1: Create TypeScript Feature

**Request**: "Create a new authentication module"

**Result**:
```
src/dev/auth/
├── index.ts
├── types.ts
└── utils.ts

src/Tests/auth/
├── index.test.ts
└── utils.test.ts
```

### Example 2: Create Python Project

**Request**: "Create a data-processor project in Python"

**Result**:
```
src/dev/data-processor/
├── __init__.py
├── processor.py
└── utils.py

src/Tests/data-processor/
├── test_processor.py
└── test_utils.py
```

### Example 3: Add Feature to Existing Project

**Request**: "Add a validation module to the auth project"

**Result**:
```
src/dev/auth/validation/
└── index.ts

src/Tests/auth/validation/
└── index.test.ts
```

## Rules

1. **Never** place source files directly in `src/`
2. **Never** place tests outside `src/Tests/`
3. **Always** mirror paths between dev and test
4. **Always** use lowercase with hyphens for directory names

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
