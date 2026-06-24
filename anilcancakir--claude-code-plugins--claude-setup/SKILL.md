---
name: claude-setup
description: CLAUDE.md generation patterns using project.md and brand.md context. Use when creating CLAUDE.md, running /my_setup, optimizing Claude Code configuration, or asking about technical documentation for Claude Code. Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Claude Setup Skill

Patterns and templates for creating optimized CLAUDE.md files that integrate project.md and brand.md context.

## Workflow Integration

```
project.md (what) → brand.md (identity) → CLAUDE.md (how)
```

CLAUDE.md should:
- Reference project.md for project overview
- Reference brand.md for design guidelines
- Focus on TECHNICAL configuration for Claude

## Core Principles

### 1. Context-Aware Generation
- Read project.md for project identity and goals
- Read brand.md for design and voice guidelines
- Focus CLAUDE.md on technical commands and patterns

### 2. Signal Over Noise
- Only document non-obvious information
- Avoid restating what's clear from code
- Focus on gotchas, patterns, and decisions

### 3. Token Budget Awareness
| Project Size | Target Tokens | Max Lines |
|--------------|---------------|-----------|
| Small (<10k LOC) | 500-1000 | 50-100 |
| Medium (10-50k LOC) | 1000-2000 | 100-200 |
| Large (>50k LOC) | 2000-4000 | 200-300 |

### 4. Structured Sections
Use XML-style tags for parseability:
- `<ARCHITECTURE>` - Technical architecture decisions
- `<CONVENTIONS>` - Project-specific code patterns
- `<TOOLS>` - MCP servers and preferences
- `<TESTING>` - Test framework and patterns
- `<COMMANDS>` - Quick reference table
- `<BRAND>` - Quick reference from brand.md
- `<GOTCHAS>` - Proven solutions to problems

## CLAUDE.md Structure

```markdown
# [Project Name]

[One-line description - from project.md or detected]

## Quick Reference

| Command | Description |
|---------|-------------|
| `[cmd]` | [purpose]   |

For project overview, see [project.md](project.md).
For brand guidelines, see [brand.md](brand.md).

<ARCHITECTURE>
## Architecture

[Technical architecture - patterns, layers, data flow]
[Reference project.md for project goals and features]
</ARCHITECTURE>

<CONVENTIONS>
## Conventions

### Naming
- [File naming pattern]
- [Class/function naming]

### Code Organization
- [Feature vs layer structure]
- [Import ordering]
</CONVENTIONS>

<TESTING>
## Testing

- **Framework**: [PHPUnit/Jest/Pest/etc.]
- **Run**: `[command]`
- **Coverage**: `[command]`

### Patterns
- [Test organization]
- [Mocking approach]
</TESTING>

<TOOLS>
## Available Tools

### Research
- `WebSearch` - Documentation lookup
- `mcp__context7__query-docs` - Framework docs

### Development
- `mcp__github__*` - Issue/PR management
- `Bash` - Build and test commands

### Tool Preferences
- [When to use which tool]
</TOOLS>

<BRAND>
## Design Guidelines

Reference: See [brand.md](brand.md) for full guidelines.

**Quick Reference:**
- Archetype: [From brand.md]
- Primary: [HEX from brand.md]
- Font: [From brand.md]
- Voice: [Key attributes from brand.md]
</BRAND>

<GOTCHAS>
## Gotchas

### [Issue Name]
**Problem**: [Description]
**Solution**: [Fix with file reference]
</GOTCHAS>
```

## Context Integration

### From project.md

Extract and use:
- **Project name**: For CLAUDE.md header
- **Description**: For one-line summary
- **Target audience**: To calibrate technical detail level
- **Core features**: To prioritize what to document

### From brand.md

Extract and reference:
- **Archetype**: For voice calibration in generated content
- **Primary color**: For quick design reference
- **Font**: For typography reference
- **Voice attributes**: For documentation tone

### In CLAUDE.md

- Add references to project.md and brand.md
- Include quick reference <BRAND> section
- Don't duplicate full content from either file

## Stack-Specific Patterns

See reference files for detailed templates:
- [references/stack-patterns.md](references/stack-patterns.md) - Laravel, Flutter, Vue patterns
- [references/claude-md-templates.md](references/claude-md-templates.md) - Complete templates
- [references/rules-templates.md](references/rules-templates.md) - Path-specific rules
- [references/mcp-catalog.md](references/mcp-catalog.md) - MCP server and LSP recommendations

## Smart Merge Strategy

When updating existing CLAUDE.md:

1. **Check context files** - Read project.md and brand.md first
2. **Identify custom sections** - Content not from templates
3. **Preserve custom content** - Never delete user additions
4. **Add missing sections** - Insert template sections that don't exist
5. **Add references** - Link to project.md and brand.md
6. **Show diff** - Always preview changes before applying

## Quality Checklist

Before finalizing CLAUDE.md:

- [ ] References project.md (if exists)
- [ ] References brand.md (if exists)
- [ ] Commands are copy-paste ready and tested
- [ ] No obvious/redundant information
- [ ] Architecture section explains WHY not just WHAT
- [ ] Gotchas have concrete solutions
- [ ] Token count within budget
- [ ] All file paths verified
- [ ] MCP tools documented if available
- [ ] Uses XML section tags

## Anti-Patterns to Avoid

1. **Duplicating project.md** - Reference it, don't copy
2. **Duplicating brand.md** - Reference it, don't copy
3. **Generic advice** - "Write clean code" adds no value
4. **Obvious info** - "This is a Laravel app" when composer.json exists
5. **Verbose explanations** - One line per concept when possible
6. **Outdated commands** - Verify all commands work
7. **No structure** - Always use XML sections

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
