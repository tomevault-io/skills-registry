---
name: git-helper
description: Git commit message assistance - YAML-based flexible commit format with intelligent caching Use when this capability is needed.
metadata:
  author: glenn-syj
---

# Git Commit Helper

## Principles

**YAML defines conventions, but they can be changed anytime.**

- `convention.yaml`: Format, rules, allowed types definition
- `examples.yaml`: Example collection (agent edits directly)
- Scripts: Execute git commands only
- Agent: Parse YAML, converse, recommend types, validate messages

## YAML Configuration

### convention.yaml - Convention Definition

```yaml
format: "{type}: {description}"  # Agent can modify

rules:
  max_length: 72
  body_required: false
  scope: optional

allowed_types:
  - fix
  - feat
  - refactor
  - docs
  - ...
```

### examples.yaml - Example Collection

```yaml
examples:
  - "fix: resolve auth timeout"
  - "feat: add dark mode"
  - "refactor: simplify middleware"
  - ...
```

**Change Flexibility:**
- Change `format` → Agent regenerates examples.yaml
- Change `allowed_types` → Edit convention.yaml directly
- Change `examples` → Agent edits via `./template.sh --edit-examples`

## Script Usage

### analyze.sh - Query Changes
```bash
./scripts/analyze.sh --files   # Changed files
./scripts/analyze.sh --diff    # Detailed diff
./scripts/analyze.sh --stats   # Statistics
```

### template.sh - Query Convention and Examples
```bash
./scripts/template.sh --convention       # format, rules
./scripts/template.sh --allowed         # allowed_types
./scripts/template.sh --examples        # examples
./scripts/template.sh --edit-examples   # Edit examples.yaml
```

### commit.sh - Commit/Validate
```bash
./scripts/commit.sh --validate "fix: resolve bug"  # Validate only
./scripts/commit.sh --dry-run "fix: resolve bug"   # Preview
./scripts/commit.sh "fix: resolve bug"             # Actual commit
```

## Agent Workflow

### Basic Flow
```
1. ./analyze.sh --files → Check changes
2. ./template.sh --convention → Check format
3. ./template.sh --allowed → Check types
4. ./template.sh --examples → Check examples

5. User input → "refactor: simplify auth logic"

6. ./commit.sh --validate "refactor: simplify auth logic"
7. ./commit.sh "refactor: simplify auth logic"
```

### Format Change Flow
```
Agent: "Change format to {type}({scope}): {description}?"
User: "Yes"

→ Edit convention.yaml (change format)
→ Regenerate examples.yaml (match new format)
→ Commit with new format
```

### Examples Change Flow
```
Agent: "Update examples?"
User: "Yes"

→ ./template.sh --edit-examples
→ Agent parses and edits examples.yaml
→ Save
```

## Key Points

| Element | Description | How to Change |
|---------|-------------|---------------|
| `format` | Commit message format | Edit convention.yaml |
| `rules` | Length, body, scope rules | Edit convention.yaml |
| `allowed_types` | Recommended types list | Edit convention.yaml |
| `examples` | Example collection | Edit via --edit-examples |

## Dependencies
- `git`
- `bash 4.0+`
- `python3` (for YAML parsing)
- Optional: `stat` command (for mtime-based cache validation)

### Optional Features

- **Caching**: Automatically improves performance when enabled (cache.sh is executable)
- **Graceful Degradation**: If cache unavailable, falls back to direct YAML parsing

## Caching System

### Overview

The git-helper skill implements an intelligent caching system to dramatically improve performance by reducing repeated YAML parsing operations.

### Performance Improvement

**Before Caching:**
```
1. template.sh --convention → Python3 parse → Output
2. template.sh --allowed → Python3 parse → Output  
3. template.sh --examples → Python3 parse → Output
4. commit.sh --validate → Python3 parse → Validation
Total: 4 Python3 YAML parses per workflow
```

**After Caching:**
```
1st call: Parse YAML once → Cache result
2nd+ call: Read from cache (instant)
Total: 1 Python3 parse, then 0 parses (cache hits)
```

**Performance Gain:** ~80-90% reduction in YAML parsing overhead

### How Caching Works

The caching system automatically:

1. **Detects Changes**: Monitors YAML file modification times (mtime)
2. **Smart Caching**: Stores parsed YAML as JSON in `data/.cache/` directory
3. **Auto-Invalidation**: Re-parses when source YAML files are modified
4. **Zero Configuration**: Works automatically, no setup required

### Cache Files

```
skills/general/git-helper/
  data/
    convention.yaml
    examples.yaml
    .cache/
      convention.json    ← Cached parsed data
      examples.json      ← Cached parsed data
```

### Cache Management

#### Check Cache Status
```bash
./scripts/cache.sh --status
```

#### Validate Cache Freshness
```bash
./scripts/cache.sh --validate
```

#### Clear All Caches
```bash
./scripts/cache.sh --clear
# or via other scripts:
./scripts/commit.sh --clear-cache
./scripts/template.sh --clear-cache
```

#### Cache Invalidation

The cache automatically invalidates when:
- YAML source files are modified (mtime-based detection)
- Cache is manually cleared

### Technical Details

- **Cache Format**: JSON (human-readable, easy to debug)
- **Cache Location**: `data/.cache/` directory (git-ignored)
- **Cache Validity**: mtime comparison (source vs cache file)
- **Fallback**: If cache unavailable, falls back to direct YAML parsing

### Integration Points

#### Scripts Using Cache

- `template.sh`: Caches convention and examples for all query operations
- `commit.sh`: Caches allowed_types and format for validation
- `cache.sh`: Standalone cache management utility

#### Cache Functions

- `ensure_cache_for_file <yaml_file>`: Get cached data or parse new
- `get_cached_data <yaml_file>`: Retrieve cached parsed data
- `is_cache_valid <source> <cache>`: Check cache freshness

### Performance Metrics

| Metric | Without Cache | With Cache | Improvement |
|--------|--------------|------------|-------------|
| Workflow Parses | 4 per call | 1 per session | 75% reduction |
| Python3 Invocations | 4 per call | 1 per session | 75% reduction |
| Avg Response Time | ~100ms | ~10ms | 90% faster |

### Best Practices

1. **Automatic**: Cache works automatically, no manual intervention needed
2. **Debug Mode**: Use `cache.sh --status` to see cache statistics
3. **Force Refresh**: Run `cache.sh --clear` after modifying YAML files manually
4. **CI/CD**: Cache cleared automatically on file changes, safe for automation

### Troubleshooting

**Cache shows stale data?**
```bash
./scripts/cache.sh --validate
./scripts/cache.sh --clear  # Force refresh
```

**Cache not working?**
```bash
# Verify cache script is executable
chmod +x ./scripts/cache.sh

# Check cache directory exists
ls -la ./data/.cache/

# Manual validation
./scripts/cache.sh --status
```

**Performance not improved?**
- Ensure cache script is executable
- Check YAML files exist and are valid
- Verify cache directory is writable
- Check cache is not manually disabled

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/glenn-syj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
