---
name: documentation-standards
description: Technical documentation standards for APIs, architecture, and developer guides Use when this capability is needed.
metadata:
  author: seqis
---

# Documentation Standards Skill

## Overview

Standards and requirements for project documentation. Defines required files, format standards, content guidelines, and automated documentation triggers.

## Type

standards

## When to Invoke

**Trigger keywords:** documentation, docs, readme, API reference, changelog, roadmap, schemas, data flow

**Invoke when:**
- Starting a new project
- Reviewing documentation completeness
- Before commits (doc check)
- After significant code changes
- Creating API documentation

## Required `./docs/` Files

Every project should have:

| File | Purpose |
|------|---------|
| `ROADMAP.md` | Overview, features, architecture, future plans |
| `API_REFERENCE.md` | Endpoints, schemas, examples |
| `DATA_FLOW.md` | Architecture, patterns, data interactions |
| `SCHEMAS.md` | Database schemas, models, validation rules |
| `BUG_REFERENCE.md` | Known issues, causes, solutions, prevention |
| `VERSION_LOG.md` | Release history, changes by version |
| `memory-archive/` | Historical CLAUDE.md versions (via /prune) |

## Format & Style Standards

### Headers
- Use ##/### hierarchically
- Don't skip levels (## then ####)

### Metadata
- Include "Last Updated" date
- Include version number where relevant

### Line Length
- Maximum 100 characters per line
- Break long lines for readability

### Code Blocks
- Always specify language
- Include runnable examples where possible

### Examples
- Provide practical, copy-paste examples
- Show both input and expected output

## Content Guidelines

1. **Write for future developers** - Assume no context
2. **Explain "why" not just "what"** - Rationale matters
3. **Cross-link related docs** - Connect concepts
4. **Stay focused on topic** - One doc, one purpose
5. **Version significant changes** - Track evolution

## Automated Documentation Triggers

| Event | Action |
|-------|--------|
| Bug fix | Update BUG_REFERENCE.md (description, cause, solution, prevention) |
| New feature | Update ROADMAP.md (description, architecture, APIs) |
| API change | Update API_REFERENCE.md (endpoints, breaking changes, migration) |
| Architecture change | Update DATA_FLOW.md |
| Database change | Update SCHEMAS.md |
| Before commit | Check all docs for accuracy |
| `/changes` command | Update VERSION_LOG.md |
| `/prune` command | Archive to memory-archive/ |

## `/changes` Checklist

Before running `/changes`:
- [ ] APIs documented in API_REFERENCE
- [ ] Bugs documented in BUG_REFERENCE
- [ ] Features documented in ROADMAP
- [ ] VERSION_LOG entry prepared
- [ ] Cross-references valid
- [ ] Examples still work

## Documentation Review Questions

When reviewing docs, ask:
1. Could a new developer understand this?
2. Are there undocumented APIs or features?
3. Are examples current and working?
4. Are there broken cross-references?
5. Is the version history accurate?

## Template: New Documentation File

```markdown
# [Title]

*Last Updated: YYYY-MM-DD | Version: X.Y.Z*

## Overview

Brief description of what this document covers.

## [Main Sections]

Content organized hierarchically.

## Examples

Practical examples with code blocks.

## Related Documentation

- [[Link to related doc]]
- [[Another related doc]]

---
*Maintained by: [owner]*
```

## Integration

Works with:
- `/changes` command - Updates VERSION_LOG
- `/prune` command - Archives to memory-archive/
- `systematic-debugging` skill - BUG_REFERENCE updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
