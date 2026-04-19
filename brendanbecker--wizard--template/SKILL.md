---
name: skill-name
description: > Use when this capability is needed.
metadata:
  author: brendanbecker
---

# Skill Name

## Overview

Brief description of what this skill does and when it's used. This section
helps both humans and the agent understand the skill's purpose.

## Parameters

Extract these from the work item:

- **param_name** (required): Description of this parameter
- **another_param** (optional): Description with default behavior if omitted

## Instructions

1. Read the AGENT.md file in the target repository to understand patterns
2. Examine existing similar configurations in `path/to/examples/`
3. Create the required files:
   - `path/to/new/file.yaml` - Purpose of this file
   - `path/to/another/file.yaml` - Purpose of this file
4. Update any parent configuration files (e.g., kustomization.yaml)
5. Run validation using `scripts/validate.sh`

## Standards

All changes MUST follow these standards:

- Standard 1: Description
- Standard 2: Description
- Standard 3: Description

## Validation

Before creating the PR, verify:

```bash
# Run from workspace root
scripts/validate.sh {param_name}

# Or manually:
kubectl apply --dry-run=client -f path/to/file.yaml
yamllint path/to/file.yaml
```

## Do NOT

- Modify unrelated files
- Skip required configurations
- Hardcode values that should be parameterized
- Create resources outside the designated paths

## Examples

See `references/` for complete examples:

- `references/example-simple.md` - Basic usage
- `references/example-advanced.md` - Complex scenario with multiple params

### Quick Example

**Request**: "Create X named 'foo' in production"

**Parameters extracted**:
- param_name: foo
- environment: production

**Files created**:
```
clusters/production/foo/
├── resource.yaml
└── kustomization.yaml
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brendanbecker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
