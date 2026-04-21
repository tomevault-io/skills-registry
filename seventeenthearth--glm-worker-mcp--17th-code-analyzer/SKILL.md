---
name: 17th-code-analyzer
description: | Use when this capability is needed.
metadata:
  author: seventeenthearth
---

# Code Analyzer

Analyze staged code and surrounding context for duplications, dead code, and optimization opportunities using GPT-5.3-Codex.

## Usage

```
/code-analyzer              → Codex high (default)
/code-analyzer deep         → Codex xhigh
```

## Argument Parsing

**Keywords (reserved):**
- `deep` → Use xhigh effort instead of high

### Parsing Logic

```
args = split(input)
effort = "high"

for arg in args:
    if arg == "deep":
        effort = "xhigh"
```

### Result Mapping

| Input | effort |
|-------|--------|
| (none) | high |
| `deep` | xhigh |

## Analysis Scope

| Scope | Description |
|-------|-------------|
| Staged files | Production + test code in git staged |
| Surrounding code | Same package/directory, related modules |

## Analysis Items

| Item | Description |
|------|-------------|
| Duplications | Similar code patterns, copy-paste code |
| Dead code | Unused functions, imports, variables |
| Optimizations | Performance improvements, inefficient patterns |
| Consistency | Pattern alignment with existing codebase |

## Implementation

Invoke the `17th-code-analyzer` subagent:

```
Task(
    description="Analyze code quality ({effort})",
    subagent_type="17th-code-analyzer",
    prompt="""
Analyze code quality for staged changes and surrounding code.

Configuration:
- Effort: {effort}  (high or xhigh)
- Project root: {project_root}
""",
    run_in_background=true
)
```

## Auto-Trigger

When skill auto-triggers (no args), uses defaults:
- **Effort**: high

## Output

- Report: `.kkachi/{branch_name}/completed/code-analyzer-{epoch_timestamp}.md`
- Summary: duplications, dead code, optimizations found
- Recommendations: prioritized action items

## Examples

| Input | effort |
|-------|--------|
| (none) | high |
| `deep` | xhigh |

## Notes

- Uses GPT-5.3-Codex for all analysis
- Analyzes staged changes + surrounding code
- Supports Go, Python, Dart, TypeScript, JavaScript
- Results verified before reporting
- Ensure `.kkachi/` is in `.gitignore`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seventeenthearth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
