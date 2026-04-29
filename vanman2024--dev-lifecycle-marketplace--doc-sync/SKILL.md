---
name: doc-sync
description: Documentation synchronization using Mem0 for tracking relationships between specs, architecture, ADRs, and roadmap Use when this capability is needed.
metadata:
  author: vanman2024
---

# Documentation Sync Skill

## Overview

This skill provides tools and scripts for intelligently synchronizing documentation across the dev-lifecycle-marketplace using Mem0 for relationship tracking.

**Purpose:** Keep specs, project files (README, roadmap/*.json, specs/), ADRs, and roadmap interconnected and updated when dependencies change.

## Key Concepts

### Documentation Types
- **Specs** (`specs/{number}-{name}/spec.md`) - Feature specifications derived from architecture
- **Architecture** (`roadmap/*.json and specs/`) - System design and component specifications
- **ADRs** (`docs/adr/*.md`) - Architecture Decision Records
- **Roadmap** (`docs/ROADMAP.md`) - Project timeline and milestones

### Relationship Tracking with Mem0

Uses Mem0 OSS (in-memory Qdrant) to store natural language relationships:

```python
# Example memory
"Specification 001 (user-authentication) is derived from
architecture/security.md sections #authentication and #jwt-tokens,
and references ADR-0008 OAuth decision"
```

### Benefits Over JSON/Database
- ✅ Natural language queries: "What specs depend on security.md?"
- ✅ No complex schemas or parsing
- ✅ Easy to understand and modify
- ✅ Conversational interface
- ✅ Local-first (no cloud dependencies)

## Available Scripts

### 1. `scripts/sync-to-mem0.py`

Scans documentation and populates Mem0 with relationships.

**Usage:**
```bash
python scripts/sync-to-mem0.py
```

**What it does:**
- Scans all `specs/*/spec.md` files
- Parses architecture references: `@docs/architecture/file.md#section`
- Parses dependencies: `dependencies: [001, 002]`
- Creates Mem0 memories describing relationships
- Uses user_id for project isolation

### 2. `scripts/query-relationships.py`

Query Mem0 for documentation relationships.

**Usage:**
```bash
# Find specs that depend on a doc
python scripts/query-relationships.py "What specs depend on architecture/security.md?"

# Find all references to an ADR
python scripts/query-relationships.py "Which specs reference ADR-0008?"

# Get spec dependencies
python scripts/query-relationships.py "What does spec 001 depend on?"
```

### 3. `scripts/validate-docs.py`

Validate documentation consistency using Mem0.

**Usage:**
```bash
python scripts/validate-docs.py
```

**Checks:**
- Broken architecture references
- Missing dependency specs
- Circular dependencies
- Orphaned documents

## Templates

### Memory Templates

**Spec Memory:**
```
Specification {number} ({name}) is derived from architecture/{file}.md
sections {sections}, references ADR-{numbers}, and depends on specs {deps}.
Status: {status}. Last updated: {date}
```

**Architecture Memory:**
```
Architecture document {file}.md has sections: {sections}.
Section {section} is referenced by specs {spec_numbers}
```

**Derivation Chain:**
```
When architecture/{file}.md #{section} changes, these specs need review:
{spec_list}
```

## Examples

### Example 1: Sync All Documentation

```bash
# Navigate to project root
cd /path/to/dev-lifecycle-marketplace

# Run sync to populate Mem0
python plugins/planning/skills/doc-sync/scripts/sync-to-mem0.py

# Output:
# ✅ Scanned 15 specs
# ✅ Found 42 architecture references
# ✅ Created 57 memories in Mem0
# 📊 Project: dev-lifecycle-marketplace
```

### Example 2: Query Impact of Changes

```bash
# Check what's affected by changing security.md
python plugins/planning/skills/doc-sync/scripts/query-relationships.py \
  "What specs are derived from architecture/security.md?"

# Output:
# Specs affected by architecture/security.md:
# - 001-user-authentication (sections: #authentication, #jwt-tokens)
# - 005-admin-panel (sections: #rls-policies)
# - 012-sso-integration (sections: #oauth)
```

### Example 3: Validate Before Deployment

```bash
# Check documentation consistency
python plugins/planning/skills/doc-sync/scripts/validate-docs.py

# Output:
# ✅ All architecture references valid
# ⚠️  Spec 003 references missing ADR-0015
# ⚠️  Circular dependency: 007 → 008 → 007
# ❌ Broken reference: @docs/architecture/deleted.md
```

## Configuration

### Mem0 Setup

The scripts use Mem0 OSS with in-memory Qdrant (no external dependencies):

```python
config = {
    "vector_store": {
        "provider": "qdrant",
        "config": {
            "collection_name": "documentation",
            "host": "memory",  # in-memory mode
        }
    }
}
```

### Project Isolation

Uses `user_id` for multi-project support:

```python
# Add memory for specific project
m.add(memory_text, user_id="dev-lifecycle-marketplace")

# Query specific project
m.search(query, user_id="dev-lifecycle-marketplace")
```

## Integration with Planning Commands

This skill powers these planning commands:

- `/planning:doc-sync` - Populate Mem0 with current documentation
- `/planning:impact-analysis <doc-path>` - Show what's affected by changes
- `/planning:validate-docs` - Check documentation consistency
- `/planning:update-docs` - Interactive sync with user approval

## Best Practices

1. **Run sync after major changes:**
   ```bash
   # After updating architecture or ADRs
   python scripts/sync-to-mem0.py
   ```

2. **Check impact before modifying shared docs:**
   ```bash
   # Before editing architecture/security.md
   python scripts/query-relationships.py "specs depending on security.md"
   ```

3. **Validate before commits:**
   ```bash
   # Add to pre-commit hook
   python scripts/validate-docs.py || exit 1
   ```

4. **Use natural language queries:**
   - "What specs need updating if I change auth flow?"
   - "Which ADRs does spec 001 reference?"
   - "Show me all dependencies for the user module"

## Troubleshooting

### Mem0 Not Installed

```bash
# Install in virtual environment
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install mem0ai
```

### Import Errors

Ensure you're in the virtual environment where Mem0 is installed:

```bash
source /tmp/mem0-env/bin/activate
python scripts/sync-to-mem0.py
```

### No Memories Found

Run the sync script first to populate Mem0:

```bash
python scripts/sync-to-mem0.py
```

## Future Enhancements

- [ ] Auto-sync on file changes (git hooks)
- [ ] Web UI for visualizing relationships
- [ ] Slack/Discord notifications for affected docs
- [ ] Integration with CI/CD for validation
- [ ] Export to Mermaid diagrams
- [ ] Graph memory for complex relationships

## References

- Mem0 OSS Documentation: https://docs.mem0.ai/open-source/overview
- Planning Plugin: `plugins/planning/README.md`
- Spec Management: `plugins/planning/skills/spec-management/`
- Architecture Patterns: `plugins/planning/skills/architecture-patterns/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vanman2024) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
