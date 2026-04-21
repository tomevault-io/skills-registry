---
name: opencode-mastery
description: Complete OpenCode knowledge base with live documentation access. Use for questions about OpenCode CLI, skills, plugins, tools, agents, SDK, or configuration. Automatically searches official docs via Context7 API with local fallback. Use when this capability is needed.
metadata:
  author: digi4care
---

# OpenCode Mastery

I have direct access to OpenCode documentation via tools.

## When to Use Me

Use me for:

- Questions about OpenCode CLI commands and options
- How to create/use skills, plugins, tools, agents
- SDK documentation and API reference
- Configuration and setup questions
- Best practices and patterns

Do not use me for:

- General programming questions
- Framework-specific questions (Svelte, React, etc.)
- Debugging your application code

## Available Tools

| Tool             | Purpose                             |
| ---------------- | ----------------------------------- |
| `searchDocs`     | Search OpenCode documentation       |
| `downloadDocs`   | Download/update local documentation |
| `memoryStatus`   | Check project memory status         |
| `memoryRemember` | Store info for future sessions      |
| `memoryCompact`  | Compact large memory files          |

## Workflow

When asked about OpenCode:

1. **Search documentation automatically**

   ```
   searchDocs({ query: "skills", source: "both", maxResults: 5 })
   ```

2. **Return accurate information**
   - Cite sources (Context7 or local)
   - Provide code examples from docs
   - Link to relevant documentation

3. **Remember important context**
   ```
   memoryRemember({ content: "User prefers TypeScript", category: "preference" })
   ```

## Documentation Sources

1. **Context7 (Primary)** - Live docs from `/anomalyco/opencode`
   - Always up-to-date
   - Semantic search
   - API-based access

2. **Local (Fallback)** - Cached docs in `~/.ai_docs/opencode/`
   - Updated via `downloadDocs` tool
   - 7-day cache TTL
   - Fuzzy search

## Memory Management

Project memory is stored in `.memory.md`:

- **Check status**: `memoryStatus()`
- **Store info**: `memoryRemember({ content: "...", category: "preference" })`
- **Compact**: `memoryCompact()` when over 50KB

Categories: `preference`, `pattern`, `correction`, `context`, `decision`

## Examples

### Search for Skills Documentation

```
searchDocs({ query: "skills SKILL.md frontmatter", maxResults: 3 })
```

### Search for Plugin Tools

```
searchDocs({ query: "plugin tool zod schema", maxResults: 5 })
```

### Remember a Preference

```
memoryRemember({
  content: "User prefers Bun over npm for JavaScript",
  category: "preference",
  priority: "high"
})
```

## References

- `references/registry.json` - Reference index
- `examples/plugins/` - Plugin templates (minimal, intermediate, advanced)
- `examples/tools/` - Tool templates (minimal, intermediate, advanced)
- `examples/skills/` - Skill templates (minimal, intermediate, advanced)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/digi4care) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
