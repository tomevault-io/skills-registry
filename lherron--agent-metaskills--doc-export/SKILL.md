---
name: doc-export
description: Create comprehensive, self-contained documentation for a codebase. Use when asked to prepare documentation for external consumption, export docs for AI tools, consolidate scattered documentation, or create a documentation package that can stand alone without source code access. Triggers include "prepare docs for", "export documentation", "consolidate docs", "create documentation package", or requests to make docs suitable for tools that cannot access source code. Use when this capability is needed.
metadata:
  author: lherron
---

# Documentation Export

Create self-contained documentation packages from codebases.

## Goal

Produce a minimal, accurate, well-organized documentation set that fully describes a system without requiring source code access.

## Process

### 1. Audit Existing Documentation

Inventory current docs:

```bash
find ./docs -name "*.md" -type f
ls -la ./docs/
```

For each file, assess:
- **Purpose**: What does it document?
- **Currency**: Does it reflect current implementation?
- **Overlap**: Does it duplicate other docs?
- **Completeness**: What's missing?

### 2. Explore the Codebase

Understand what needs documenting:

- **Domain model**: Core types, entities, relationships
- **Architecture**: Packages, layers, key design decisions
- **Commands/API**: User-facing interfaces
- **Data model**: Database schema, storage

Use grep/glob to find domain types, command definitions, schema files.

### 3. Plan Document Structure

Target 3-5 focused documents:

| Document | Purpose | Content |
|----------|---------|---------|
| **SPEC.md** | Product specification | Goals, data model, command reference, behaviors |
| **DOMAIN-MODEL.md** | Conceptual model | Business concepts, relationships, terminology |
| **CLI-REFERENCE.md** or **API-REFERENCE.md** | Usage reference | Commands/endpoints, examples, workflows |
| **ARCHITECTURE.md** | Technical architecture | Package structure, design decisions, patterns |

Adjust based on project type (CLI, library, service, etc.).

### 4. Consolidate and Rename

Apply consistent naming:
- Use `SCREAMING-KEBAB-CASE.md` (e.g., `CLI-REFERENCE.md`)
- Delete sparse/outdated files
- Merge related content into focused documents
- Remove duplicate information

### 5. Update Content

For each document:

**Spec/Reference docs:**
- Verify all commands/endpoints documented
- Update field lists to match current schema
- Fix incorrect states, types, enums
- Add missing features (check recent commits)
- Remove deprecated/unimplemented features

**Architecture docs:**
- Document actual package structure
- Explain key design decisions
- Cover data model and storage
- Note what's implemented vs planned

**Domain model:**
- Define all core concepts
- Show relationships between entities
- Clarify terminology
- Note implementation status

### 6. Verify Cross-References

Check for broken references:

```bash
grep -r "docs/[A-Z]" ./docs/
grep -rn "\.md" ./docs/ | grep -v "^Binary"
```

Update all internal links to match renamed files.

### 7. Final Review

Checklist:
- [ ] Each document has a clear, single purpose
- [ ] No duplicate information across documents
- [ ] All cross-references valid
- [ ] Naming convention consistent
- [ ] Outdated content removed
- [ ] Missing features documented
- [ ] Implementation status noted where relevant
- [ ] Documents are self-contained (no broken image refs, etc.)

## Document Guidelines

### SPEC.md
- Comprehensive product specification
- Include: goals, data model, command/API reference, behaviors
- This is the authoritative reference
- 30-50KB typical

### DOMAIN-MODEL.md
- Conceptual, not implementation-focused
- Define business terms and relationships
- Include diagrams (Mermaid) if helpful
- Note what's implemented vs aspirational
- 10-15KB typical

### CLI-REFERENCE.md / API-REFERENCE.md
- Feature-oriented, not exhaustive
- Include common workflows and examples
- Group by task, not alphabetically
- 15-20KB typical

### ARCHITECTURE.md
- Technical decisions and rationale
- Package/module structure
- Key patterns and conventions
- Design decisions explained
- 10-15KB typical

## Anti-patterns

- Creating README.md or CHANGELOG.md (not useful for external consumption)
- Keeping sparse files (< 20 lines of content)
- Duplicating content across documents
- Referencing non-existent files or images
- Including implementation details in conceptual docs
- Documenting aspirational features as implemented

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lherron) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
