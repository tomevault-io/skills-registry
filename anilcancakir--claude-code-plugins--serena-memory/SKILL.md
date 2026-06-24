---
name: serena-memory
description: | Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Serena Memory Management

## Memory Operations

| Operation | Tool |
|-----------|------|
| Save knowledge | `write_memory(key, content)` |
| Update existing | `edit_memory(key, content)` |
| Load knowledge | `read_memory(key)` |
| List all | `list_memories()` |
| Remove | `delete_memory(key)` |

## Recommended Memory Keys

| Key | Purpose |
|-----|---------|
| `architecture_overview` | System architecture |
| `code_conventions` | Coding standards |
| `recent_changes` | Latest commits |
| `known_issues` | Bugs and workarounds |
| `test_patterns` | Testing conventions |
| `session_context` | Current task (preserved during compaction) |

## When to Update Memories

1. **After exploration**: Save architectural insights
2. **After debugging**: Document root causes and fixes
3. **After refactoring**: Record successful patterns
4. **Before compaction**: Preserve current task context
5. **After commits**: Summarize significant changes

## Example: Architecture Memory

```
write_memory(
  key="architecture_overview",
  content="""
# Project Architecture

## Layers
- API: FastAPI routes in src/api/
- Services: Business logic in src/services/
- Models: SQLAlchemy models in src/models/

## Key Patterns
- Dependency injection via FastAPI Depends
- Repository pattern for data access

## Entry Points
- Main app: src/main.py
- CLI: src/cli.py
"""
)
```

## Example: Code Conventions Memory

```
write_memory(
  key="code_conventions",
  content="""
# Code Conventions

- snake_case for functions/variables
- PascalCase for classes
- Type hints required for public functions
- Docstrings in Google style
- Tests in tests/ mirror src/ structure
"""
)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
