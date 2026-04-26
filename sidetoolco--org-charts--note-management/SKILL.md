---
name: note-management
description: Specialized skill for organizing, searching, and maintaining the extensive notes system (17,000+ markdown files). Use when creating, organizing, searching, or maintaining documentation across business, personal, and project notes. Use when this capability is needed.
metadata:
  author: sidetoolco
---

# Note Management

Specialized skill for managing and organizing an extensive documentation system with 17,000+ markdown files across multiple domains.

## When to use this skill

Use this skill when:
- Creating new documentation or notes
- Searching for existing notes or information
- Organizing and categorizing documentation
- Maintaining WARP.md and context.md files
- Archiving outdated materials
- Cross-referencing related documents
- Establishing documentation standards
- Managing knowledge base

## System Overview

### Structure
**Location**: `notes/` directory (3.3MB, 17,000+ files)

**Main Categories**:
- **Business Operations**: kleva, sidetool documentation
- **Client Work**: proposals, project notes
- **Internal**: team notes, strategy, learning
- **Archive**: historical and merged documents
- **Communications**: emails, email-writer templates
- **Financial**: personal-finance, mel-inversiones
- **Meetings**: meeting notes and agendas
- **Projects**: project-specific documentation

### Key Subdirectories

**Active Business**:
- `kleva/` (27 files): Business documentation, roadmaps, sales materials
- `sidetool/` (33 files): Product documentation
- `proposals/` (21 files): Client proposals and pitches

**Communication**:
- `email-writer/` (75 files): Email templates and drafts
- `emails/` (68 files): Important correspondence

**Strategic**:
- `strategy/` (15 files): Strategic planning documents
- `team/` (12 files): Team coordination and management

**Personal**:
- `personal-finance/` (15 files): Financial planning
- `mel-inversiones/` (9 files): Investment notes
- `learning/` (5 files): Learning resources

**Reference**:
- `archive/` (26 files): Historical documents
- `meetings/` (9 files): Meeting records
- `ogpm/` (11 files): OGPM-related notes

## Organization Principles

### File Naming Conventions
- Use lowercase with hyphens: `sales-sequence-v2.md`
- Include dates for versioned content: `proposal-2024-12-15.md`
- Be descriptive but concise: `kleva-linkedin-campaign-q1.md`
- Use consistent prefixes within categories

### Directory Structure
- Group by topic, not document type
- Keep hierarchy shallow (max 2-3 levels)
- Use clear, self-explanatory folder names
- Maintain parallel structure across similar areas

### Metadata Files
Each major directory should have:
- **WARP.md**: Overview, last updated, contents, status
- **context.md**: Detailed purpose, organization, usage notes

## Common Workflows

### Creating New Notes

1. **Determine category**
   - What domain does this belong to?
   - Is there an existing subdirectory?
   - Should this create a new category?

2. **Choose location**
   - Active work: appropriate subdirectory
   - Reference material: archive if not current
   - Project-specific: under projects/

3. **Name appropriately**
   - Follow naming conventions
   - Make it searchable
   - Include version/date if relevant

4. **Add metadata**
   - Frontmatter if structured
   - Clear headings and sections
   - Links to related documents

5. **Update parent WARP.md**
   - Add to contents list if significant
   - Update last modified date
   - Note any structural changes

### Finding Existing Notes

**Search Strategies**:
1. Check relevant subdirectory first
2. Use grep for keyword search
3. Check WARP.md files for overview
4. Look in archive/ for historical content
5. Cross-reference related areas

**Search Commands**:
```bash
# Find files by name
find notes/ -name "*keyword*"

# Search content
grep -r "search term" notes/

# Recent files
find notes/ -type f -mtime -7
```

### Organizing Content

**Regular Maintenance**:
1. Review and update WARP.md files quarterly
2. Archive completed projects
3. Consolidate duplicate information
4. Remove outdated materials
5. Update cross-references

**Archiving Process**:
1. Identify completed/outdated content
2. Move to `archive/` with date prefix
3. Update any links pointing to archived content
4. Document archival reason in WARP.md
5. Consider creating summary if extensive

### Cross-Referencing

**Link Patterns**:
- Relative links within notes: `[text](../category/file.md)`
- Absolute paths for cross-domain: Full path from `notes/`
- Reference Code repos: `Code/holding/kleva/...`
- External resources: Full URLs with context

**Maintaining Links**:
- Check links when moving files
- Update references in WARP.md files
- Document related materials
- Create "see also" sections

## Documentation Standards

### Markdown Structure

```markdown
# Title

## Overview
Brief description and purpose

## Contents
- Section 1
- Section 2

## Key Information
Main content organized logically

## Related Documents
- [Related item](path/to/file.md)

## Status
Current state and next actions
```

### WARP.md Template

```markdown
# WARP - notes/[category]

## folder overview
Brief description of purpose and contents

## last updated
YYYY-MM-DD

## contents
- Key files and subdirectories
- Major documents

## related to
- Cross-references to other areas

## status
active/archived/under review
```

### Context.md Template

```markdown
# context - notes/[category]

## purpose
Detailed explanation of folder purpose

## organization system
How content is structured and why

## key areas
Major subcategories and their focus

## usage
How to use these notes effectively

## maintenance
Update schedule and responsibilities

## status
Current state
```

## Best Practices

### Content Quality
- Write for future you, not just present you
- Include context and rationale
- Use clear headings and structure
- Add dates to time-sensitive content
- Link to related materials

### Maintenance
- Review regularly, don't just accumulate
- Archive rather than delete
- Update WARP.md files when structure changes
- Keep README or index files current
- Version important documents

### Searchability
- Use descriptive titles
- Include key terms in content
- Add tags or categories when helpful
- Create index files for large collections
- Maintain consistent terminology

### Efficiency
- Template common document types
- Reuse good structures
- Create shortcuts for frequent tasks
- Batch similar operations
- Automate repetitive tasks

## Integration with Other Systems

### Code Repositories
Notes often reference:
- `holding/kleva/`: Active Kleva projects
- `holding/sidetool/`: Sidetool development
- `personal-projects/`: Personal project code
- Link bidirectionally when relevant

### Version Control
- Commit notes regularly
- Use meaningful commit messages
- Tag major milestones
- Branch for major reorganizations
- Document structural changes

### Backup and Sync
- iCloud sync active
- Git provides version history
- Consider export for critical docs
- Maintain redundancy for essential content

## Common Patterns

### Meeting Notes
Location: `notes/meetings/`
Structure:
- Date and attendees
- Agenda items
- Discussion points
- Action items with owners
- Follow-up needed

### Proposals
Location: `notes/proposals/`
Structure:
- Executive summary
- Problem statement
- Proposed solution
- Timeline and deliverables
- Budget and terms
- Next steps

### Project Documentation
Location: `notes/projects/`
Structure:
- Project overview
- Goals and success metrics
- Timeline
- Resources and dependencies
- Status updates
- Decisions and rationale

## Troubleshooting

### Too Many Files
- Use find with filters
- Check modification dates
- Review by category
- Consider consolidation
- Archive aggressively

### Duplicate Information
- Search for similar content
- Consolidate into single source
- Link to canonical version
- Archive redundant copies
- Update cross-references

### Lost Content
- Check archive/
- Search by date modified
- Review git history
- Check related directories
- Look in similar categories

## Related Skills
- Use `context-manager` for large documentation projects
- Use `kleva-business` for Kleva-specific notes
- Use `health-data-analysis` for health documentation
- Combine with agent skills for content creation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sidetoolco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
