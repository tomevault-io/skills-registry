---
name: gemini-cli-learning
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Gemini CLI Learning

> Learn to configure and optimize Gemini CLI

## Topics

### 1. GEMINI.md Configuration

The context file that provides instructions to Gemini:

```markdown
# Project Context

## Overview
This project uses Next.js 14 with App Router.

## Coding Standards
- Use TypeScript
- Follow ESLint rules
- Use Prettier for formatting

## Architecture
[Describe your architecture here]
```

### 2. Extensions

Install and manage extensions:

```bash
# Install from GitHub
gemini extensions install username/extension-name

# List installed
gemini extensions list

# Update all
gemini extensions update
```

### 3. Hooks

Customize Gemini CLI behavior:

```json
{
  "hooks": {
    "SessionStart": [...],
    "BeforeAgent": [...],
    "BeforeTool": [...],
    "AfterTool": [...]
  }
}
```

### 4. Agent Skills

Create custom skills:

```yaml
---
name: my-skill
description: Does something useful
license: Apache-2.0
---

# My Skill

Instructions for the skill...
```

### 5. MCP Servers

Connect to external services:

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": ["run", "ghcr.io/github/github-mcp-server"]
    }
  }
}
```

## Commands

```bash
# Learn about specific topic
/gemini-cli-learning hooks

# Setup new project
/gemini-cli-learning setup

# Get help
/gemini-cli-learning help
```

## Resources

- [Gemini CLI Docs](https://geminicli.com/docs/)
- [Extensions Guide](https://geminicli.com/docs/extensions/)
- [Hooks Reference](https://geminicli.com/docs/hooks/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
