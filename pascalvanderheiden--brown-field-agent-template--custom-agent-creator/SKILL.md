---
name: custom-agent-creator
description: Create GitHub Copilot Custom Agents with proper YAML frontmatter, tools configuration, and prompt engineering Use when this capability is needed.
metadata:
  author: pascalvanderheiden
---

# GitHub Copilot Custom Agent Creator

Create specialized GitHub Copilot agents with tailored expertise for specific development tasks. Custom agents are Markdown files with YAML frontmatter that define the agent's identity, capabilities, tool access, and behavioral instructions.

## Quick Start

### Agent File Location

| Level | Location | Scope |
|-------|----------|-------|
| Repository | `.github/agents/*.agent.md` | Single repository |
| Organization | `.github-private/agents/*.agent.md` | All repos in org |
| Enterprise | `.github-private/agents/*.agent.md` | All repos in enterprise |
| VS Code User | User profile folder | All workspaces |
| VS Code Workspace | `.github/agents/*.agent.md` | Current workspace |

### File Naming

- Extension: `.agent.md` or just `.md`
- Allowed characters: `.`, `-`, `_`, `a-z`, `A-Z`, `0-9`
- Examples: `test-specialist.agent.md`, `code-reviewer.agent.md`

## Agent Profile Structure

```markdown
---
name: agent-name
description: Required description of the agent's purpose
tools: ["read", "edit", "search"]
target: vscode
---

Your agent's prompt and instructions go here.
Define behavior, expertise, and guidelines.
Maximum 30,000 characters.
```

## YAML Frontmatter Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `name` | string | No | Display name (defaults to filename) |
| `description` | string | **Yes** | Agent's purpose and capabilities |
| `tools` | list/string | No | Available tools (defaults to all) |
| `target` | string | No | `vscode` or `github-copilot` (defaults to both) |
| `infer` | boolean | No | Auto-select based on task context (defaults to true) |
| `model` | string | No | AI model to use (IDE only) |
| `mcp-servers` | object | No | MCP server config (org/enterprise only) |
| `metadata` | object | No | Custom annotation data |

## Tools Configuration

### Enable All Tools (Default)

```yaml
# Option 1: Omit tools property entirely
---
name: my-agent
description: Agent with all tools available
---

# Option 2: Explicit wildcard
---
name: my-agent
description: Agent with all tools available
tools: ["*"]
---
```

### Enable Specific Tools

```yaml
---
name: my-agent
description: Read-only agent
tools: ["read", "search"]
---
```

### Disable All Tools

```yaml
---
name: my-agent
description: Chat-only agent with no tools
tools: []
---
```

### Tool Aliases

| Alias | Alternatives | Description |
|-------|--------------|-------------|
| `execute` | `shell`, `Bash`, `powershell` | Execute shell commands |
| `read` | `Read`, `NotebookRead`, `view` | Read file contents |
| `edit` | `Edit`, `MultiEdit`, `Write`, `NotebookEdit` | Edit files |
| `search` | `Grep`, `Glob` | Search files or text |
| `agent` | `custom-agent`, `Task` | Invoke other custom agents |
| `web` | `WebSearch`, `WebFetch` | Fetch URLs, web search |
| `todo` | `TodoWrite` | Manage task lists (VS Code) |

### MCP Server Tools

```yaml
# All tools from an MCP server
tools: ["my-mcp-server/*"]

# Specific tool from MCP server
tools: ["my-mcp-server/specific-tool"]

# Built-in MCP servers
tools: ["github/*", "playwright/*"]

# VS Code extension tools
tools: ["azure.some-extension/some-tool"]
```

### Built-in MCP Servers

| Server | Description |
|--------|-------------|
| `github` | Read-only GitHub tools (scoped to source repo) |
| `playwright` | Browser automation (localhost only) |

## Writing Effective Prompts

### Prompt Structure Best Practices

1. **Role Definition**: Start with who/what the agent is
2. **Responsibilities**: List core responsibilities
3. **Guidelines**: Provide specific behavioral guidance
4. **Constraints**: Define boundaries and limitations
5. **Output Format**: Specify expected output style

### Example Prompt Structure

```markdown
---
name: my-specialist
description: Description here
---

You are a [role] specialized in [domain]. Your responsibilities:

- [Primary responsibility]
- [Secondary responsibility]
- [Additional responsibilities]

## Guidelines

- [Guideline 1]
- [Guideline 2]

## Constraints

- [What NOT to do]
- [Boundaries]

## Output Format

[How to format responses]
```

## Example Agent Profiles

### Testing Specialist

```markdown
---
name: test-specialist
description: Focuses on test coverage, quality, and testing best practices without modifying production code
---

You are a testing specialist focused on improving code quality through comprehensive testing. Your responsibilities:

- Analyze existing tests and identify coverage gaps
- Write unit tests, integration tests, and end-to-end tests following best practices
- Review test quality and suggest improvements for maintainability
- Ensure tests are isolated, deterministic, and well-documented
- Focus only on test files and avoid modifying production code unless specifically requested

Always include clear test descriptions and use appropriate testing patterns for the language and framework.
```

### Implementation Planner

```markdown
---
name: implementation-planner
description: Creates detailed implementation plans and technical specifications in markdown format
tools: ["read", "search", "edit"]
---

You are a technical planning specialist focused on creating comprehensive implementation plans. Your responsibilities:

- Analyze requirements and break them down into actionable tasks
- Create detailed technical specifications and architecture documentation
- Generate implementation plans with clear steps, dependencies, and timelines
- Document API designs, data models, and system interactions
- Create markdown files with structured plans that development teams can follow

Always structure your plans with clear headings, task breakdowns, and acceptance criteria. Include considerations for testing, deployment, and potential risks. Focus on creating thorough documentation rather than implementing code.
```

### Code Reviewer

```markdown
---
name: code-reviewer
description: Reviews code for quality, security, performance, and best practices
tools: ["read", "search"]
---

You are a senior code reviewer focused on improving code quality. Your responsibilities:

- Review code for bugs, security vulnerabilities, and performance issues
- Check adherence to coding standards and best practices
- Identify potential edge cases and error handling gaps
- Suggest improvements for readability and maintainability
- Verify proper error handling, logging, and documentation

Provide constructive feedback with specific suggestions and code examples. Prioritize critical issues over style preferences. Reference relevant documentation when applicable.
```

### Documentation Writer

```markdown
---
name: doc-writer
description: Creates and maintains comprehensive documentation for codebases
tools: ["read", "search", "edit"]
---

You are a technical documentation specialist. Your responsibilities:

- Write clear, comprehensive README files
- Create API documentation with examples
- Document architecture decisions and system design
- Write inline code comments where helpful
- Create user guides and tutorials

Use clear, concise language. Include code examples where appropriate. Structure documentation with proper headings and navigation. Keep documentation up-to-date with code changes.
```

### Security Auditor

```markdown
---
name: security-auditor
description: Identifies security vulnerabilities and recommends fixes
tools: ["read", "search"]
---

You are a security specialist focused on identifying and addressing vulnerabilities. Your responsibilities:

- Analyze code for common security vulnerabilities (OWASP Top 10)
- Review authentication and authorization implementations
- Check for sensitive data exposure and improper handling
- Identify injection vulnerabilities (SQL, XSS, command injection)
- Review dependency security and suggest updates

Provide detailed security reports with severity ratings. Include remediation steps and code examples for fixes. Reference relevant security standards and best practices.
```

## MCP Server Configuration (Org/Enterprise Only)

```yaml
---
name: my-agent-with-mcp
description: Agent with custom MCP server
tools: ["read", "edit", "custom-mcp/tool-1"]
mcp-servers:
  custom-mcp:
    type: local
    command: some-command
    args: ["--arg1", "--arg2"]
    tools: ["*"]
    env:
      API_KEY: ${{ secrets.API_KEY }}
      BASE_URL: ${{ var.BASE_URL }}
---

Agent prompt here...
```

### Environment Variable Syntax

| Syntax | Description |
|--------|-------------|
| `$VAR_NAME` | Environment variable |
| `${VAR_NAME}` | Environment variable (Claude Code syntax) |
| `${{ secrets.VAR_NAME }}` | Repository secret |
| `${{ var.VAR_NAME }}` | Repository variable |

## Target Environments

```yaml
# VS Code only
target: vscode

# GitHub.com only
target: github-copilot

# Both environments (default)
# Omit the target property
```

## Agent Processing Rules

### Naming Conflicts (Precedence)

1. Repository-level (highest priority)
2. Organization-level
3. Enterprise-level (lowest priority)

### Versioning

- Based on Git commit SHAs
- Create branches/tags for different versions
- Pull request interactions use consistent version

## Best Practices

### Agent Design

1. **Single Responsibility**: Focus each agent on one domain
2. **Clear Description**: Make the purpose immediately obvious
3. **Minimal Tools**: Only enable tools the agent needs
4. **Specific Prompts**: Provide detailed behavioral guidance
5. **Examples**: Include examples of expected output

### Prompt Writing

1. **Be Specific**: Avoid vague instructions
2. **Set Boundaries**: Define what NOT to do
3. **Format Output**: Specify expected response format
4. **Include Context**: Provide domain-specific knowledge
5. **Test Iteratively**: Refine based on actual usage

### Tool Selection

1. **Least Privilege**: Only enable necessary tools
2. **Read-Only First**: Start with read/search, add edit later
3. **Document Rationale**: Explain why tools are needed
4. **Test Tool Usage**: Verify agent uses tools correctly

## Resources

- [Creating Custom Agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents)
- [Custom Agents Configuration](https://docs.github.com/en/copilot/reference/custom-agents-configuration)
- [Custom Agents in VS Code](https://code.visualstudio.com/docs/copilot/customization/custom-agents)
- [Awesome Copilot Agents](https://github.com/github/awesome-copilot/tree/main/agents)

## File References

For detailed documentation, see:

- [references/yaml-properties.md](references/yaml-properties.md) - Complete YAML property reference
- [references/tools-reference.md](references/tools-reference.md) - All available tools and aliases
- [references/mcp-configuration.md](references/mcp-configuration.md) - MCP server configuration guide
- [examples/test-specialist.agent.md](examples/test-specialist.agent.md) - Testing agent template
- [examples/code-reviewer.agent.md](examples/code-reviewer.agent.md) - Code review agent template
- [examples/security-auditor.agent.md](examples/security-auditor.agent.md) - Security audit agent template
- [scripts/validate-agent.ts](scripts/validate-agent.ts) - Agent file validation utility

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pascalvanderheiden) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
