---
name: create-agent
description: Create well-formed Claude agents with proper YAML frontmatter, system prompts, and examples. Use when user wants to create a new agent, customize Claude behavior, or needs help structuring an agent definition. Use when this capability is needed.
metadata:
  author: terrazul-ai
---

# Create Agent Skill

This skill guides you through creating a well-structured Claude agent with proper frontmatter, system prompt, and examples.

## When to Use This Skill

Activate this skill when the user wants to:
- Create a new Claude agent
- Customize Claude's behavior for specific tasks
- Structure an agent definition file
- Understand agent components (frontmatter, prompts, examples)

## Agent Creation Workflow

### 1. Understand Agent Purpose

Ask the user (or infer from context):
- What is the agent's primary role/expertise?
- What specific tasks should it handle?
- Does it need to be read-only or can it write files?
- What level of capability is needed? (quick responses vs deep analysis)

### 2. Determine Agent Configuration

**Model Selection**:
- `opus` - Complex reasoning, deep analysis, architectural decisions
- `sonnet` - Balanced tasks, code review, general development (most common)
- `haiku` - Quick responses, simple tasks, high volume

**Tool Selection** (based on agent role):
- **Read-only agents**: Read, Grep, Glob
- **Research agents**: + mcp__context7__*, mcp__exa__*, WebSearch, WebFetch
- **Code agents**: + Write, Edit, mcp__ide__*, mcp__lsp-api__*
- **Review agents**: + mcp__ide__getDiagnostics, mcp__github__*
- **Interactive agents**: + AskUserQuestion, TodoWrite

**Color Selection** (for UI identification):
- `cyan` - Architecture/design
- `purple` - Code review/quality
- `green` - Testing/verification
- `blue` - Documentation/writing
- `red` - Debugging/troubleshooting
- `yellow` - Research/exploration

### 3. Generate Agent File

Create a markdown file with this structure:

```markdown
---
name: descriptive-name
description: Brief 1-2 sentence description of when to use this agent
model: sonnet
color: purple
tools:
  - Read
  - Grep
  - Glob
  - [additional tools as needed]
---

# Role: [Agent Name]

[2-3 sentence description of agent's role and expertise]

## Core Responsibilities

1. **[Responsibility 1]** - [Description]
2. **[Responsibility 2]** - [Description]
3. **[Responsibility 3]** - [Description]

## [Methodology/Process Section]

[Detailed instructions on how the agent should approach tasks]

## Success Criteria

A successful [outcome] should:
- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]
```

### 4. Validation Checklist

Before saving, verify:
- [ ] YAML frontmatter is valid (use triple dashes `---`)
- [ ] Name is lowercase with hyphens (e.g., `code-reviewer`)
- [ ] Description is concise and actionable (< 200 chars)
- [ ] Model is appropriate for task complexity
- [ ] Tools match agent capabilities
- [ ] System prompt defines clear role
- [ ] Core responsibilities are specific (not vague)
- [ ] Success criteria are measurable

## Agent File Naming

Save agent files as:
- `.claude/agents/[name].md` (for local project)
- Or in your agent package's `templates/claude/agents/` directory

Use descriptive names that match the `name` field in frontmatter.

## Quick Start Templates

For common agent types, consider using these templates as starting points:
- `templates/basic-agent.md` - Generic structure
- `templates/research-agent.md` - Read-only with research tools
- `templates/code-agent.md` - Implementation with write access
- `templates/review-agent.md` - Code review and quality

Access templates with: "Use the research agent template"

## Reference Materials

For detailed information, consult:
- `reference.md` - Complete agent structure documentation
- `examples.md` - Real-world agent examples with pattern analysis

## Tips for Effective Agents

1. **Be Specific**: Clear, focused role beats general-purpose
2. **Match Model to Task**: Don't use opus for simple tasks (cost consideration)
3. **Appropriate Tools**: Only include tools agent actually needs
4. **Actionable Prompts**: System prompt should give clear behavioral guidance
5. **Test with Examples**: Try agent with realistic scenarios after creation
6. **Iterate**: Refine based on actual usage patterns

## Common Patterns

**Read-Only Analysis Agent**:
- Model: sonnet
- Tools: Read, Grep, Glob, mcp__context7__*
- Use case: Code review, documentation analysis

**Implementation Agent**:
- Model: sonnet or opus
- Tools: Read, Write, Edit, Grep, Glob
- Use case: Feature development, refactoring

**Research Agent**:
- Model: sonnet
- Tools: Read, Grep, mcp__context7__*, mcp__exa__*, WebSearch
- Use case: Library research, best practices investigation

**Interactive Planning Agent**:
- Model: opus
- Tools: Read, Grep, Glob, AskUserQuestion, TodoWrite
- Use case: Architecture decisions, technical planning

## Example Invocations

Here are examples of how users might request different agents:

- "Create a code review agent for TypeScript"
- "I need an agent that helps with system architecture"
- "Make a debugging agent for Go services"
- "Create a test-driven development agent"
- "Build an agent for API documentation"

For each request, determine the appropriate model, tools, and structure based on the patterns above.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
