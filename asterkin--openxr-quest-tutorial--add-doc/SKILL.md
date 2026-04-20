---
name: add-doc
description: Register new documentation sources for Context7-based queries. Use when adding tools or SDKs to the project. Use when this capability is needed.
metadata:
  author: asterkin
---

# Add Documentation Source Skill

Register new documentation sources for Context7-based queries.

## When to Use

Invoke this skill when:
- Adding a new tool or SDK to the project
- Updating Context7 library ID for an existing source
- Configuring aliases for easier querying

## Usage

```bash
# Add a new documentation source
python .claude/skills/add-doc/scripts/add-source.py <name> <context7_id> "<description>" [--tokens N] [--alias NAME]
```

## Examples

```bash
# Add Android NDK documentation
python .claude/skills/add-doc/scripts/add-source.py ndk "websites/android_ndk" "Android NDK native development" --tokens 3000

# Add with alias
python .claude/skills/add-doc/scripts/add-source.py cpp "websites/cppreference" "C++ 20 language and STL" --alias c++
```

## Workflow

1. Find the Context7 library ID at [context7.com](https://context7.com)
2. Run `add-source.py` with appropriate parameters
3. Verify with `doc-query/scripts/list-sources.py`
4. Test query with `doc-query/scripts/query.py`

## Configuration

Sources are stored in `.claude/doc-sources.toml`.

## Requirements

- Python 3.14+

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/asterkin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
