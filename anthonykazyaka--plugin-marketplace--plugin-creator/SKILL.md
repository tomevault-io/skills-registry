---
name: plugin-creator
description: Guide for creating and managing Claude Code plugins. Use when creating a new plugin, adding plugin components (commands, agents, skills, hooks, MCP servers), or validating plugin structure and configuration. Use when this capability is needed.
metadata:
  author: anthonykazyaka
---

# Plugin Creator

This skill provides comprehensive guidance for creating, developing, and managing Claude Code plugins.

## About Plugins

Plugins are modular packages that extend Claude Code's capabilities by bundling multiple components into a single installable unit. Unlike individual skills, plugins can include:

- **Commands**: Custom slash commands for repeatable workflows
- **Agents**: Specialized subagents for autonomous task handling
- **Skills**: Model-invoked capabilities that trigger based on context
- **Hooks**: Event handlers that respond to system events
- **MCP Servers**: External tool integrations via Model Context Protocol

## Plugin Development Workflow

Follow these steps to create a new plugin:

1. Understand plugin requirements and scope
2. Initialize plugin structure
3. Add components (commands, agents, skills, hooks, MCP)
4. Validate plugin structure
5. Test plugin locally
6. Distribute plugin

### Step 1: Understanding Plugin Requirements

Before creating a plugin, clarify:

- What capabilities should the plugin provide?
- Which components are needed (commands, agents, skills, hooks, MCP)?
- Who is the target audience (personal, team, public)?
- What existing tools or workflows will it enhance?

Ask questions to gather concrete examples:
- "What tasks should this plugin help with?"
- "Do you need custom slash commands for specific workflows?"
- "Should Claude automatically use these capabilities (skills) or should they be user-invoked (commands)?"
- "Do you need to integrate with external tools (MCP servers)?"

### Step 2: Initialize Plugin Structure

Create basic plugin directory with `.claude-plugin/plugin.json` containing: name, description, version, and author fields.

**For detailed initialization steps:**
- See [references/plugin-structure.md](references/plugin-structure.md) for complete setup instructions and field requirements

### Step 3: Add Plugin Components

Based on requirements, add the appropriate components. See references for detailed guidance on each component type:

#### Adding Skills

Skills are model-invoked capabilities that Claude uses automatically based on task context.

**When to use:** For capabilities that should activate automatically (e.g., PDF processing, spreadsheet analysis, code review)

**How to add:**
1. Create `skills/` directory at plugin root
2. For each skill, create `skills/skill-name/SKILL.md`
3. Follow skill-creator guidance for SKILL.md structure

**Example:**
```
my-plugin/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    ├── pdf-processor/
    │   └── SKILL.md
    └── data-analyzer/
        └── SKILL.md
```

See [references/skills-in-plugins.md](references/skills-in-plugins.md) for complete guidance.

#### Adding Commands

Commands are custom slash commands for repeatable workflows.

**When to use:** For user-initiated workflows that should be explicitly invoked (e.g., /deploy, /review-pr, /generate-report)

**How to add:**
1. Create `commands/` directory at plugin root
2. For each command, create `commands/command-name.md`
3. Add YAML frontmatter with description
4. Write instructions for Claude

**Example command file** (`commands/deploy.md`):
```markdown
---
description: Deploy application to production environment
---

# Deploy Command

When this command is invoked, perform these steps:

1. Run pre-deployment checks
2. Build the application
3. Deploy to production
4. Verify deployment success
```

Commands support arguments: `/deploy staging` or `/deploy production`

See [references/commands.md](references/commands.md) for advanced command features.

#### Adding Agents

Agents are specialized subagents that Claude can invoke for specific task types.

**When to use:** For complex multi-step tasks that benefit from dedicated focus (e.g., code review agent, testing agent)

**How to add:**
1. Create `agents/` directory at plugin root
2. For each agent, create `agents/agent-name.md`
3. Define agent's role, capabilities, and workflow

See [references/agents.md](references/agents.md) for agent development patterns.

#### Adding Hooks

Hooks are event handlers that execute in response to system events.

**When to use:** For automation triggered by events (e.g., linting after edits, logging tool usage)

**How to add:**
1. Create `hooks/` directory at plugin root
2. Create `hooks/hooks.json` with hook configuration
3. Define event types, matchers, and commands

**Note:** Hooks are configured through Claude Code settings, not traditionally through plugin files. The hooks.json in plugins primarily serves as documentation or defaults.

See [references/hooks.md](references/hooks.md) for available events and configuration.

#### Adding MCP Servers

MCP servers connect Claude to external tools and services.

**When to use:** For integrating external APIs, databases, or services (e.g., GitHub API, Slack, database access)

**How to add:**
1. Create `.mcp.json` at plugin root
2. Configure MCP server connection details
3. Document server capabilities

See [references/mcp-integration.md](references/mcp-integration.md) for MCP configuration.

### Step 4: Validate Plugin Structure

Use the validation script to check plugin structure:

```bash
python3 utils/validate_plugin.py path/to/my-plugin
```

The validator checks:
- plugin.json format and required fields
- Component directory structure
- SKILL.md frontmatter for skills
- Command file format
- Overall plugin organization

Fix any validation errors before proceeding.

### Step 5: Adding Plugin to Marketplace Configuration

After creating and validating a new plugin, check if there is a marketplace configuration file that should be updated to include it:

**For marketplace repositories:**
- Check if `.claude-plugin/marketplace.json` exists in the repository
- If it exists, the new plugin may need to be added to the `plugins` array
- Each marketplace entry includes: name, source, description, version, and author

**Example marketplace.json entry:**
```json
{
  "name": "my-plugin",
  "source": {
    "source": "github",
    "repo": "username/repo-name"
  },
  "description": "Plugin description",
  "version": "1.0.0",
  "author": {
    "name": "Author Name"
  }
}
```

**Important:** Ask the user how they want to handle the newly created plugin:
- Add it to the marketplace configuration for distribution
- Test it out locally first before adding to marketplace
- Keep it as a standalone plugin (not in marketplace)
- Another option

The user's preference will determine whether marketplace configuration updates are needed.

### Step 6: Test Plugin Locally

Install the plugin locally for testing:

```bash
# Install from local directory
claude plugin install /path/to/my-plugin
```

Test all components:
- For skills: Trigger tasks that should activate the skills
- For commands: Run slash commands with `/command-name`
- For agents: Invoke tasks that should use the agents
- For hooks: Trigger events to verify hook execution

Iterate based on testing feedback.

### Step 7: Distribute Plugin

Choose distribution method:

**Option 1: Git Repository**
```bash
# Users install from git URL
claude plugin install https://github.com/username/my-plugin
```

**Option 2: Plugin Marketplace**
- Submit to marketplace for wider distribution
- Follow marketplace submission guidelines
- Provide documentation and examples

**Option 3: Local/Team Distribution**
- Share plugin directory with team
- Install from local path or shared network location

## Best Practices & Common Patterns

**For detailed information:**
- See [references/plugin-structure.md](references/plugin-structure.md) for:
  - Common plugin patterns (single-skill, multi-skill, command-heavy, full-featured)
  - Best practices for scope, naming, versioning, and documentation
  - Component integration guidelines

## Resources

This skill includes detailed reference documentation for each plugin component:

- **references/skills-in-plugins.md**: Comprehensive guide for adding skills to plugins
- **references/commands.md**: Advanced command features and patterns
- **references/agents.md**: Agent development and configuration
- **references/hooks.md**: Hook events and configuration
- **references/mcp-integration.md**: MCP server integration guide
- **references/plugin-json-schema.md**: Complete plugin.json schema reference

Load these references as needed when working on specific components.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anthonykazyaka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
