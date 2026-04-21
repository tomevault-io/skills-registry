---
name: create-skill
description: Create and manage Claude Code skills following Anthropic best practices. Use when creating new skills, modifying skill-rules.json, understanding trigger patterns, working with hooks, debugging skill activation, or implementing progressive disclosure. Covers skill structure, YAML frontmatter (including context fork, allowed-tools, agent), trigger types (keywords, intent patterns, file paths, content patterns), enforcement levels (block, suggest, warn), hook mechanisms (UserPromptSubmit, PreToolUse), session tracking, and the 500-line rule. Use when this capability is needed.
metadata:
  author: runxgalee
---

## Purpose

Help developers create and manage Claude Code skills following Anthropic best practices. This skill provides comprehensive guidance for:

- **Skill Structure**: YAML frontmatter configuration, content organization, and file placement
- **Trigger Patterns**: Keywords, intent patterns, file paths, and content patterns for automatic activation
- **Advanced Features**: Context forking, tool restrictions, agent delegation, and session tracking
- **Enforcement Levels**: Block, suggest, and warn behaviors for different use cases

## When to Use

Use this skill when you need to:

- **Create a new skill** from scratch with proper structure and configuration
- **Understand trigger patterns** - how skills are automatically activated based on user input
- **Configure skill behavior** - context fork, allowed-tools, agent specification
- **Debug skill activation** - troubleshoot why a skill isn't triggering as expected
- **Implement guardrails** - create blocking skills that prevent common mistakes
- **Design domain skills** - create advisory skills for specific technical areas
- **Follow the 500-line rule** - structure large skills with progressive disclosure

## Key Information

### Skill File Structure

Skills are stored in `.claude/skills/{skill-name}/SKILL.md` with required YAML frontmatter:

```yaml
---
name: skill-name              # Required: Unique identifier
description: Keywords here    # Required: Max 1024 chars, include trigger terms
context: fork                 # Optional: Run in isolated sub-agent context
agent: specialist-name        # Optional: Delegate to specific agent
allowed-tools:                # Optional: Restrict available tools
  - Read
  - Grep
  - Glob
user-invocable: true          # Optional: Show in slash command menu (default: true)
---
```

### Trigger Pattern Best Practices

The `description` field is critical for skill activation. Include:

1. **Primary keywords**: Direct terms users might use (e.g., "skill", "create skill")
2. **Related concepts**: Associated topics (e.g., "trigger patterns", "hooks")
3. **File patterns**: Relevant paths (e.g., "SKILL.md", "skill-rules.json")
4. **Action verbs**: What users want to do (e.g., "creating", "debugging", "implementing")

### The 500-Line Rule

SKILL.md files should stay under 500 lines. For comprehensive skills:

1. Keep core guidance in SKILL.md
2. Create reference files for detailed documentation
3. Add table of contents to reference files > 100 lines
4. Use progressive disclosure - start simple, provide depth when needed

## Skill Types

### 1. Guardrail Skills

**Purpose:** Enforce critical best practices that prevent errors

**Characteristics:**
- Type: `"guardrail"`
- Enforcement: `"block"`
- Priority: `"critical"` or `"high"`
- Block file edits until skill used
- Prevent common mistakes (column names, critical errors)
- Session-aware (don't repeat nag in same session)

**Examples:**
- `database-verification` - Verify table/column names before Prisma queries
- `frontend-dev-guidelines` - Enforce React/TypeScript patterns

**When to Use:**
- Mistakes that cause runtime errors
- Data integrity concerns
- Critical compatibility issues

### 2. Domain Skills

**Purpose:** Provide comprehensive guidance for specific areas

**Characteristics:**
- Type: `"domain"`
- Enforcement: `"suggest"`
- Priority: `"high"` or `"medium"`
- Advisory, not mandatory
- Topic or domain-specific
- Comprehensive documentation

**Examples:**
- `backend-dev-guidelines` - Node.js/Express/TypeScript patterns
- `frontend-dev-guidelines` - React/TypeScript best practices
- `error-tracking` - Sentry integration guidance

**When to Use:**
- Complex systems requiring deep knowledge
- Best practices documentation
- Architectural patterns
- How-to guides

---

## Creating a New Skill

### Step 1: Create Skill File

**Location:** `.claude/skills/{skill-name}/SKILL.md`

**Basic Template:**
```markdown
---
name: my-new-skill
description: Brief description including keywords that trigger this skill. Mention topics, file types, and use cases. Be explicit about trigger terms.
context: fork           # Run in isolated sub-agent context
agent: specialist-name  # Specify agent type to execute this skill (optional)
allowed-tools:          # Restrict tools available to this skill (optional)
---

# My New Skill

## Purpose
What this skill helps with

## When to Use
Specific scenarios and conditions

## Key Information
The actual guidance, documentation, patterns, examples
```

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Skill identifier (required) |
| `description` | string | Trigger keywords and description (required, max 1024 chars) |
| `context` | string | Set to `fork` to run in isolated sub-agent context |
| `agent` | string | Agent type to execute this skill (e.g., `react-specialist`) |
| `allowed-tools` | list | YAML list of permitted tools for this skill |
| `user-invocable` | boolean | Whether to show in slash command menu (default: true) |

**When to use each field:**

- **`context: fork`**: Use for research/exploration skills that should not affect main conversation state. Great for skills that generate lots of intermediate output or may need to be discarded.

- **`agent`**: Use when a specific specialist agent should handle this skill. Available agents include:
    - `react-specialist` - React/frontend tasks
    - `typescript-specialist` - TypeScript tasks
    - `postgres-specialist` - Database tasks
    - `go-specialist` - Go development
    - `graphql-architect` - GraphQL tasks
    - `security-specialist` - Security audits
    - `debug-specialist` - Debugging tasks
    - `code-reviewer` - Code review tasks
    - And more in `.claude/agents/`

- **`allowed-tools`**: Use to restrict which tools the skill can access. Useful for:
    - Read-only skills that shouldn't modify files
    - Research skills that only need search tools
    - Security-sensitive skills with limited permissions

**Best Practices:**
- ✅ **Name**: Lowercase, hyphens, gerund form (verb + -ing) preferred
- ✅ **Description**: Include ALL trigger keywords/phrases (max 1024 chars)
- ✅ **Content**: Under 500 lines - use reference files for details
- ✅ **Examples**: Real code examples
- ✅ **Structure**: Clear headings, lists, code blocks
- ✅ **Context fork**: Use for exploration/research to keep main context clean
- ✅ **Agent**: Specify when domain expertise is needed

### Step 2: Follow Anthropic Best Practices

✅ Keep SKILL.md under 500 lines
✅ Use progressive disclosure with reference files
✅ Add table of contents to reference files > 100 lines
✅ Write detailed description with trigger keywords
✅ Test with 3+ real scenarios before documenting
✅ Iterate based on actual usage

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/runxgalee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
