---
name: convert-skill-to-gemini
description: Converts Claude SKILL.md files to Gemini-compatible format. Use when user wants to use a Claude skill with Gemini CLI, or needs to make skills cross-platform compatible.
metadata:
  author: terrazul-ai
---

# Convert Skill to Gemini

This skill helps you convert Claude Code skills (SKILL.md format) to Gemini CLI compatible format.

## When to Use

Activate this skill when the user wants to:
- Convert a Claude skill to work with Gemini
- Make skills cross-platform compatible
- Port skills from skillregistry.io to Gemini
- Understand differences between Claude and Gemini skill formats

## Conversion Workflow

### 1. Identify Source Skill

The source skill can be:
- Local: `.claude/skills/[skill-name]/SKILL.md`
- Package: `templates/claude/skills/[skill-name]/SKILL.md`
- URL: From skillregistry.io

Read the source SKILL.md file to understand:
- Frontmatter fields (name, description, allowed-tools)
- Instructions and structure
- Referenced files (reference.md, examples.md, templates/)

### 2. Analyze for Compatibility

Check for Claude-specific elements that need conversion:

**Compatible (works in both)**:
- Markdown instructions
- Lists and headers
- Code examples
- Reference to additional files

**Needs Adjustment**:
- `allowed-tools` field (different tool names in Gemini)
- Claude-specific tool references in instructions
- References to `.claude/` paths

**Not Portable**:
- Claude-specific MCP server references
- Agent delegation instructions (Gemini doesn't support agents)

### 3. Transform Frontmatter

**Claude Format**:
```yaml
---
name: skill-name
description: Description text
version: "1.0.0"
allowed-tools:
  - Read
  - Grep
  - Glob
---
```

**Gemini Format**:
```yaml
---
name: skill-name
description: Description text
version: "1.0.0"
allowed-tools:
  - read_file
  - search_files
  - list_files
---
```

### 4. Tool Name Mapping

| Claude Tool | Gemini Equivalent |
|-------------|-------------------|
| Read | read_file |
| Write | write_file |
| Edit | edit_file |
| Glob | list_files |
| Grep | search_files |
| Bash | run_shell_command |
| WebSearch | web_search |
| WebFetch | web_fetch |

### 5. Update Instructions

Replace Claude-specific references:
- `.claude/` → `.gemini/`
- Claude Code → Gemini CLI
- Agent references → Remove or note as unsupported

### 6. Save Converted Skill

**Output Location**:
- Local: `.gemini/skills/[skill-name]/SKILL.md`
- Package: `templates/gemini/skills/[skill-name]/SKILL.md`

**Copy Additional Files**:
- `reference.md` - Usually compatible as-is
- `examples.md` - Update tool references if needed
- `templates/` - Usually compatible as-is

### 7. Verify Conversion

Check the converted skill:
- [ ] Valid YAML frontmatter
- [ ] Tool names are Gemini-compatible
- [ ] No Claude-specific references in body
- [ ] Path references updated
- [ ] Works when invoked in Gemini CLI

## Reference Materials

For detailed information, consult:
- `reference.md` - Complete tool mapping and conversion details
- `examples.md` - Before/after conversion examples

## Tips for Conversion

1. **Keep Instructions Generic**: Write instructions that work for any AI assistant
2. **Document Limitations**: Note features that don't convert (agents)
3. **Test Both Platforms**: Verify skill works in both Claude and Gemini
4. **Consider Shared Skills**: For packages, skills in `templates/claude/skills/` can be shared to Gemini via agents.toml configuration

## Example Invocations

Here are examples of how users might request conversions:

- "Convert the code-review skill to Gemini format"
- "Make my typescript-guidelines skill work with Gemini"
- "Port this skill from skillregistry.io to Gemini"
- "What changes are needed to use this Claude skill in Gemini?"

## Limitations

### Features Not Portable to Gemini

1. **Agents**: Gemini doesn't support subagents
2. **MCP Servers**: Different configuration format
3. **Some Tools**: Not all Claude tools have Gemini equivalents

### Workarounds

- **Agents**: Document as manual process instead
- **MCP Servers**: Create separate Gemini MCP config
- **Missing Tools**: Check if Gemini has alternative

## Platform Notes

### Claude Skills
- Location: `.claude/skills/` or `templates/claude/skills/`
- Format: Markdown with YAML frontmatter
- Tools: Claude Code tool names

### Gemini Skills
- Location: `.gemini/skills/` or `templates/gemini/skills/`
- Format: Markdown with YAML frontmatter (same structure)
- Tools: Gemini CLI tool names

### Shared Skills (Packages)

In package agents.toml, you can reference Claude skills for Gemini:
```toml
[exports.gemini]
skillsDir = "templates/claude/skills"  # Share Claude skills
```

The CLI handles symlinking. Only convert when tool names differ.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
