---
name: claude-to-gemini-mapping
description: This skill should be used when the user asks to "convert Claude plugin to Gemini", "map Claude components to Gemini", "translate plugin format", "convert commands to TOML", "convert skills to GEMINI.md", or needs to understand how Claude Code plugin components map to Gemini CLI extension equivalents. Use when this capability is needed.
metadata:
  author: orbruno
---

# Claude to Gemini Mapping

Comprehensive mapping rules for converting Claude Code plugins to Gemini CLI extensions.

## Overview

This skill provides transformation rules for converting each Claude Code plugin component type to its Gemini CLI equivalent. The conversion handles structural differences while preserving functionality.

## Component Mapping Summary

| Claude Code | Gemini CLI | Notes |
|-------------|------------|-------|
| `.claude-plugin/plugin.json` | `gemini-extension.json` | Manifest transformation |
| `commands/*.md` | `commands/*.toml` | Format conversion |
| `skills/*/SKILL.md` | `GEMINI.md` | Context consolidation |
| `agents/*.md` | `GEMINI.md` + commands | No direct equivalent |
| `hooks/hooks.json` | `excludeTools` | Partial mapping |
| `.mcp.json` | `mcpServers` | Direct mapping |

## Manifest Conversion

### plugin.json → gemini-extension.json

**Source (Claude):**
```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "Plugin description",
  "author": {
    "name": "Author Name",
    "email": "email@example.com"
  }
}
```

**Target (Gemini):**
```json
{
  "name": "my-plugin",
  "version": "1.0.0"
}
```

**Mapping rules:**
- `name`: Direct copy (validate kebab-case)
- `version`: Direct copy
- `description`: Not supported in Gemini manifest (document in README)
- `author`: Not supported in Gemini manifest (document in README)

## Command Conversion

### Markdown → TOML

**Source format (Claude):**
```markdown
---
name: review
description: Review code for issues
allowed-tools: ["Read", "Grep"]
argument-hint: "<file-path>"
---

Review the code at the specified path for:
- Code quality issues
- Potential bugs
- Security vulnerabilities

Use the Read tool to access the file content.
```

**Target format (Gemini):**
```toml
description = "Review code for issues"
prompt = """
Review the code at the specified path for:
- Code quality issues
- Potential bugs
- Security vulnerabilities

File to review:
@{{{args}}}
"""
```

**Conversion rules:**

1. **Frontmatter → TOML fields:**
   - `description` → `description`
   - `name` → filename (command-name.toml)
   - `allowed-tools` → Not directly mappable
   - `argument-hint` → Document in description

2. **Body → prompt field:**
   - Strip tool usage instructions (Gemini handles differently)
   - Convert `${CLAUDE_PLUGIN_ROOT}` → `${extensionPath}`
   - Add `{{args}}` for argument handling

3. **Tool references:**
   - `Read` tool reference → `@{...}` file injection
   - `Bash` commands → `!{...}` shell injection
   - Other tools → Rewrite as natural language instructions

## Skill Conversion

### SKILL.md → GEMINI.md

Skills become context files in Gemini. Multiple skills consolidate into sections.

**Source (Claude skills/code-review/SKILL.md):**
```markdown
---
name: Code Review
description: This skill activates for code review tasks
---

# Code Review Guidelines

When reviewing code, check for:
1. Code style consistency
2. Error handling
3. Security issues

## Tools
Use Grep to search patterns, Read for file content.
```

**Target (Gemini GEMINI.md):**
```markdown
# Code Review Guidelines

When reviewing code, check for:
1. Code style consistency
2. Error handling
3. Security issues
```

**Conversion rules:**

1. **Frontmatter:** Strip (not used in GEMINI.md)
2. **Tool instructions:** Remove or generalize
3. **Multiple skills:** Combine into sections with headers
4. **References:** Convert to inline content or separate files

### Handling Multiple Skills

When a Claude plugin has multiple skills, create a consolidated GEMINI.md:

```markdown
# Extension Context

## Code Review Guidelines
[Content from code-review skill]

## Testing Practices
[Content from testing skill]

## Documentation Standards
[Content from documentation skill]
```

## Agent Conversion

Agents have no direct Gemini equivalent. Convert to:
1. **GEMINI.md context** for knowledge/instructions
2. **Custom commands** for triggerable actions

**Source (Claude agents/reviewer.md):**
```markdown
---
description: Code review agent that analyzes PRs
tools: ["Read", "Grep", "Bash"]
---

You are a code review expert. Analyze code for...
```

**Target options:**

**Option A - Context (GEMINI.md section):**
```markdown
## Code Review Agent Context

When performing code reviews, act as an expert reviewer.
Analyze code for quality, bugs, and security issues.
```

**Option B - Command (commands/review-pr.toml):**
```toml
description = "Run AI code review on PR changes"
prompt = """
You are a code review expert.

Analyze these PR changes for:
- Code quality
- Potential bugs
- Security vulnerabilities

Changes:
!{git diff HEAD~1}
"""
```

## Hook Conversion

Hooks have limited Gemini equivalents through `excludeTools`.

### PreToolUse Validation → excludeTools

**Source (Claude hooks/hooks.json):**
```json
{
  "PreToolUse": [{
    "matcher": "Write|Edit",
    "hooks": [{
      "type": "command",
      "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
    }]
  }]
}
```

**Target (Gemini excludeTools):**
```json
{
  "excludeTools": [
    "run_shell_command(rm -rf)",
    "run_shell_command(sudo)"
  ]
}
```

**Mapping limitations:**
- Gemini only supports blocking, not custom validation
- Complex hook logic cannot be directly converted
- Document unconverted hooks in README

### Supported Hook Patterns

| Claude Hook | Gemini Equivalent |
|-------------|-------------------|
| Block dangerous commands | `excludeTools` array |
| Tool restrictions | `coreTools` array |
| Custom validation | Not supported |
| Notifications | Not supported |
| Session events | Not supported |

## MCP Server Conversion

### .mcp.json → mcpServers

**Source (Claude .mcp.json):**
```json
{
  "mcpServers": {
    "database": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/servers/db.js"],
      "env": {
        "DB_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

**Target (Gemini gemini-extension.json):**
```json
{
  "name": "my-extension",
  "mcpServers": {
    "database": {
      "command": "node",
      "args": ["${extensionPath}/servers/db.js"],
      "env": {
        "DB_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

**Conversion rules:**
- `${CLAUDE_PLUGIN_ROOT}` → `${extensionPath}`
- Server config structure is identical
- Environment variables: Same syntax

## Path Variable Conversion

Replace all path variable references:

| Claude Code | Gemini CLI |
|-------------|------------|
| `${CLAUDE_PLUGIN_ROOT}` | `${extensionPath}` |
| (no equivalent) | `${workspacePath}` |
| (no equivalent) | `${/}` or `${pathSeparator}` |

## Conversion Workflow

To convert a Claude plugin to Gemini extension:

1. **Create manifest:** Transform plugin.json → gemini-extension.json
2. **Convert commands:** Each .md → .toml with format translation
3. **Consolidate skills:** Merge SKILL.md files → GEMINI.md
4. **Handle agents:** Convert to context sections or commands
5. **Map hooks:** Extract restrictions → excludeTools
6. **Copy MCP config:** Transform paths in server definitions
7. **Update paths:** Replace CLAUDE_PLUGIN_ROOT everywhere
8. **Create README:** Document unconverted features

## Additional Resources

### Reference Files

For detailed conversion patterns:
- **`references/conversion-patterns.md`** - Complex conversion examples
- **`references/limitations.md`** - What cannot be converted

### Example Files

Working examples in `examples/`:
- **`claude-plugin/`** - Sample Claude plugin
- **`gemini-extension/`** - Converted Gemini extension

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/orbruno) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
