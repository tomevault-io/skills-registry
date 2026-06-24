---
name: copilot-setup-audit
description: Audit a repository's Copilot CLI customization setup and suggest improvements. Use when the user wants to review their Copilot configuration, find gaps, validate agents, skills, instructions, hooks, plugins, or MCP setup, or optimize their CLI customization setup. Use when this capability is needed.
metadata:
  author: trsdn
---

# Copilot CLI Setup Audit

This skill audits a repository's GitHub Copilot CLI customization setup and provides actionable recommendations.

## When to use this skill

- User asks to audit or review their Copilot setup
- User wants to optimize their CLI customization
- User asks "what's missing from my Copilot config?"
- Setting up a new project and want best-practice guidance

## Audit Checklist

### 1. Custom Instructions

Check for existence and content of:

| File | Location | Purpose |
|------|----------|---------|
| `copilot-instructions.md` | `.github/` | Repository-wide instructions |
| `*.instructions.md` | `.github/instructions/` | Path-specific instructions |
| `AGENTS.md` | Root directory | Agent instructions |
| `CLAUDE.md` | Root directory | Cross-tool compatibility |
| `GEMINI.md` | Root directory | Cross-tool compatibility |

**Validate:**

- YAML frontmatter is valid
- `applyTo` globs match intended files (for scoped instructions)
- Instructions are specific and actionable, not vague
- No conflicting instructions between files

### 2. Custom Agents

Check for agents in:

| Location | Type |
|----------|------|
| `.github/agents/*.agent.md` | Repository-level |
| `.claude/agents/*.agent.md` | Alternative project-level |
| `~/.copilot/agents/*.agent.md` | User-level |

**Validate:**

- `description` is present (required)
- `name` is present (recommended)
- `tools` list is explicit and minimal (security best practice)
- `target`, if present, is either `github-copilot` or `vscode`
- `disable-model-invocation` and `user-invocable` are intentional when present
- `mcp-servers`, if present, uses the MCP configuration schema
- Prompt text is under 30,000 characters

### 3. Agent Skills

Check for skills in:

| Location | Type |
|----------|------|
| `.github/skills/*/SKILL.md` | Project skills |
| `.agents/skills/*/SKILL.md` | Alternative project skills |
| `.claude/skills/*/SKILL.md` | Alternative project skills |
| `~/.copilot/skills/*/SKILL.md` | Personal skills |
| `~/.agents/skills/*/SKILL.md` | Shared personal skills |

**Validate:**

- `name` and `description` are present in frontmatter
- Description is specific enough for Copilot to decide when to load
- Skill directory name matches the `name` field
- Optional `allowed-tools` is narrow and does not pre-approve shell access without a clear reason
- Optional `user-invocable` and `disable-model-invocation` fields match the intended invocation model
- Additional resources (scripts, examples) are referenced correctly

### 4. Hooks

Check for hooks in `.github/hooks/*.json`.

**Validate:**

- JSON is valid and uses `version: 1`
- Hook events are intentional (`sessionStart`, `preToolUse`, `permissionRequest`, `notification`, etc.)
- Command hooks use short timeouts and scoped working directories when possible
- HTTP hooks use HTTPS for security-sensitive events and avoid broad environment variable exposure

### 5. MCP Configuration

Check MCP configuration in:

| File | Scope |
|------|-------|
| `.github/mcp.json` | Repository |
| `.mcp.json` | Workspace |
| `~/.copilot/mcp-config.json` | User |

Validate:

- Valid JSON structure
- Built-in MCP servers are understood: GitHub, Playwright, fetch, and time
- Any additional MCP servers configured
- Tool filters are explicit (`tools`) and do not expose more tools than needed
- Secrets are not committed; use environment expansion for sensitive values
- OAuth and `/mcp auth` requirements are documented for remote servers
- OIDC is only enabled for servers designed to receive Copilot-issued identity tokens
- `filterMapping` is intentional and safe for the server output shape
- Remote servers are trusted and comply with enterprise allowlists where applicable

### 6. Plugins

Check whether the repository relies on plugin-provided skills, agents, or commands.

**Validate:**

- Plugin usage is documented for collaborators
- Plugin-provided skills do not duplicate local project skills unless intentionally overridden
- Local customizations remain usable without private plugins when possible

### 7. Permission Policy

Check CLI permission defaults and documented automation patterns.

**Validate:**

- Automation examples use explicit `--allow-tool`, `--deny-tool`, `--allow-url`, and `--deny-url` patterns
- Shell approvals are scoped, for example `shell(git:*)`, instead of blanket `shell` where possible
- Dangerous operations such as `git push`, package publishing, or destructive filesystem commands are denied or gated
- `--allow-all` / `--yolo` is documented only for trusted local use, not shared automation

### 8. Programmatic Usage

Check whether the repository documents scripted or CI use of Copilot CLI.

**Validate:**

- Prompt-mode examples use `copilot --prompt` or `-p`
- Machine-readable output uses `--output-format=json` when downstream parsing is expected
- Non-interactive runs define needed tool, path, and URL permissions up front
- Token guidance prefers `COPILOT_GITHUB_TOKEN` and least-privilege fine-grained tokens
- Shared logs do not capture secrets, full prompts, or sensitive source snippets

### 9. OpenTelemetry Monitoring

Check whether monitoring is needed for team, CI, or enterprise usage.

**Validate:**

- OTel configuration is documented when agent usage must be observable
- OTel exporter variables such as `OTEL_EXPORTER_OTLP_ENDPOINT` or `COPILOT_OTEL_FILE_EXPORTER_PATH` are intentional
- Content capture is disabled by default and only enabled in trusted environments
- Monitoring docs explain that traces can include agent turns, LLM calls, tool execution, token usage, and errors

### 10. Built-in Agents and Advanced Commands

Check whether built-in capabilities are documented before adding custom equivalents.

**Validate:**

- Built-in agents such as `explore`, `research`, `task`, `code-review`, and `general-purpose` are considered
- Advanced commands such as `/pr`, `/delegate`, `/remote`, `/keep-alive`, `/undo`, and `/rewind` are documented if used
- Experimental commands are marked as experimental where applicable

### 11. Alternative Command Format

Check for Claude-style command files if the repository uses cross-agent command workflows.

**Validate:**

- `.claude/commands/*.md` files are documented as lower-priority command-style alternatives to skills
- Command files do not duplicate higher-priority project skills unless intentionally overridden

### 12. Project-Type Detection

Detect the project type from:

- `package.json` → Node.js / JavaScript / TypeScript
- `requirements.txt` / `pyproject.toml` → Python
- `go.mod` → Go
- `Cargo.toml` → Rust
- `*.csproj` / `*.sln` → .NET
- `pom.xml` / `build.gradle` → Java

Tailor recommendations to the detected project type.

## Audit Report Format

```markdown
## Copilot CLI Setup Audit Report

### ✅ What's Working Well
- [Specific positive findings]

### ⚠️ Warnings
- [Non-critical issues]

### ❌ Issues to Fix
- [Critical problems]

### 💡 Suggestions
- [Recommended improvements]

### Recommended Next Steps
1. [Priority-ordered actions]
```

## Common Recommendations

### For all projects

- Add `.github/copilot-instructions.md` with project context, build commands, and testing approach
- Add `AGENTS.md` at root with key conventions and architecture notes
- Create at least one custom agent for the project's primary workflow

### For specific languages

**TypeScript/JavaScript:**

- Add `*.instructions.md` with `applyTo: "**/*.ts,**/*.tsx"` for TS conventions
- Consider a skill for running/debugging tests

**Python:**

- Add instructions for virtual environment, linting (ruff/flake8), and type hints
- Consider a skill for package management (pip/poetry/uv)

**Go:**

- Add instructions for `go fmt`, `go vet`, and module conventions
- Consider a skill for Go test patterns

## Diagnostic Commands

Suggest the user run these in their Copilot CLI session:

```bash
/instructions    # View which instruction files are loaded
/skills list     # See available skills
/agent           # See available agents
/env             # Show loaded instructions, MCP servers, skills, agents, plugins, LSPs, and extensions
/mcp show        # Check MCP server configuration
/plugin list     # Check installed plugins
/usage           # Review session usage metrics
/context         # Inspect context window usage
```

---
> Source: [trsdn/github-copilot-cli](https://github.com/trsdn/github-copilot-cli) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
