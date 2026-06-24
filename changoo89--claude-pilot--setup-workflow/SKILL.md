---
name: setup-workflow
description: Initial setup workflow for claude-pilot plugin - directory creation, statusline configuration, documentation sync, GitHub star request Use when this capability is needed.
metadata:
  author: changoo89
---

# SKILL: Setup (Plugin Initialization Workflow)

> **Purpose**: Initialize claude-pilot plugin for new projects with proper directory structure and configuration
> **Target**: User running `/pilot:setup` for first time in a project

---

## Quick Start

### When to Use This Skill
- First-time plugin setup in a new project
- Reset plugin directory structure
- Configure statusline for project

### Quick Reference
```bash
# Full workflow
/pilot:setup

# Steps: Directories → Statusline → Documentation → GitHub Star
```

## Execution Steps

Execute ALL steps in sequence. Do NOT pause between steps unless explicitly marked as user interaction.

### Step 1: Create Directories

**Purpose**: Initialize .pilot directory structure for plan management

```bash
mkdir -p .pilot/plan/{draft,pending,in_progress,done}
mkdir -p .pilot/state

echo "✓ .pilot directories created"
```

**Verification**:
```bash
test -d .pilot/plan/draft && echo "draft: ✓" || echo "draft: ✗"
test -d .pilot/plan/pending && echo "pending: ✓" || echo "pending: ✗"
test -d .pilot/plan/in_progress && echo "in_progress: ✓" || echo "in_progress: ✗"
test -d .pilot/plan/done && echo "done: ✓" || echo "done: ✗"
test -d .pilot/state && echo "state: ✓" || echo "state: ✗"
```

### Step 2: Configure Statusline

**Purpose**: Copy statusline script from plugin and configure settings.json

```bash
# Detect plugin installation path
PLUGIN_PATH=$(jq -r '.plugins["claude-pilot@claude-pilot"][0].installPath // empty' ~/.claude/plugins/installed_plugins.json 2>/dev/null || true)
SOURCE=""

# Always prefer plugin version to ensure latest
# Note: In distributed plugin, scripts are at $PLUGIN_PATH/scripts/ (no .claude prefix)
[[ -n "$PLUGIN_PATH" && -f "$PLUGIN_PATH/scripts/statusline.sh" ]] && SOURCE="$PLUGIN_PATH/scripts/statusline.sh"

if [[ -n "$SOURCE" ]]; then
    # Copy statusline script
    mkdir -p .claude/scripts
    cp "$SOURCE" .claude/scripts/statusline.sh
    chmod +x .claude/scripts/statusline.sh

    # Configure settings.json
    SETTINGS=".claude/settings.json"
    STATUSLINE='{"type":"command","command":"\"$CLAUDE_PROJECT_DIR\"/.claude/scripts/statusline.sh"}'

    if [[ -f "$SETTINGS" ]]; then
        # Update existing settings
        jq --argjson sl "$STATUSLINE" '. + {statusLine: $sl}' "$SETTINGS" > /tmp/settings.json && mv /tmp/settings.json "$SETTINGS"
    else
        # Create new settings
        echo "{\"statusLine\": $STATUSLINE}" > "$SETTINGS"
    fi

    # Show version
    PLUGIN_VERSION=$(jq -r '.version' "$PLUGIN_PATH/.claude-plugin/plugin.json" 2>/dev/null || echo 'unknown')
    echo "✓ Statusline configured (from plugin v$PLUGIN_VERSION)"
else
    echo "⚠ Statusline script not found in plugin, skipping"
fi
```

**Verification**:
```bash
test -f .claude/scripts/statusline.sh && echo "statusline.sh: ✓" || echo "statusline.sh: ✗"
test -f .claude/settings.json && echo "settings.json: ✓" || echo "settings.json: ✗"
```

### Step 3: Agent Teams Configuration

**Purpose**: Configure Agent Teams environment and hooks

**Display to user**:
```
🤝 Agent Teams Configuration Required:

Agent Teams is enabled by default in Claude Code. Configure hooks for quality gates:

1. Open Claude Code settings.json (Cmd+Shift+P → "Preferences: Open User Settings (JSON)")

2. Add Agent Teams hooks:

{
  "hooks": {
    "TaskCompleted": [
      {
        "matcher": "",
        "hooks": [".claude/hooks/verify-task-completion.sh"]
      }
    ],
    "TeammateIdle": [
      {
        "matcher": "",
        "hooks": [".claude/hooks/teammate-idle-check.sh"]
      }
    ]
  }
}

3. Terminal compatibility:
   - Recommended: tmux or iTerm2 for split-pane display mode
   - Fallback: in-process mode works in all terminals (VS Code, Windows Terminal, etc.)

Note: If CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 environment variable is needed,
      add to settings.json under "env" key.
```

**No action required** - informational only.

### Step 4: MCP Servers (Informational)

**Purpose**: Inform user about recommended MCP servers

**Display to user**:
```
📦 Recommended MCP Servers:
   - context7: Latest library documentation (@upstash/context7-mcp)
   - sequential-thinking: Step-by-step reasoning (@modelcontextprotocol/server-sequential-thinking)

Configuration: Add to ~/.claude.json under projects.<project-path>.mcpServers
```

**No action required** - informational only.

### Step 5: Initial Documentation (Optional)

**Purpose**: Ask user if they want to generate initial documentation

**User Interaction** (use AskUserQuestion):
- Question: "Generate initial documentation (3-tier docs sync)?"
- Options: "Yes, generate docs" / "No, skip"

**If yes**:
```bash
echo "📚 Running documentation sync..."
echo "Invoke the three-tier-docs skill to generate initial documentation."
```

**If no**:
```bash
echo "⏭️  Skipping documentation sync"
```

### Step 6: GitHub Star Request (Optional)

**Purpose**: Ask user if they want to star the repository

**User Interaction** (use AskUserQuestion):
- Question: "Star the claude-pilot repository on GitHub?"
- Options: "Yes, star the repo" / "No thanks"

**If yes**:
```bash
# Check if gh CLI is available
if command -v gh &> /dev/null; then
    if gh api -X PUT /user/starred/changoo89/claude-pilot 2>&1; then
        echo "⭐ Thank you for starring claude-pilot!"
    else
        echo "⚠️  Could not star via gh CLI"
        echo "   Manual link: https://github.com/changoo89/claude-pilot"
    fi
else
    echo "⚠️  gh CLI not found"
    echo "   Star manually: https://github.com/changoo89/claude-pilot"
fi
```

**If no**:
```bash
echo "👋 Thanks for using claude-pilot!"
```

### Step 7: Complete

**Purpose**: Show completion message and next steps

```bash
echo ""
echo "✓ claude-pilot setup complete"
echo "  Run /00_plan to start planning"
```

## What This Skill Covers

### In Scope
- Directory creation (.pilot structure)
- Statusline configuration (copy from plugin)
- MCP server recommendations
- Optional documentation generation
- Optional GitHub star request

### Out of Scope
- MCP server installation → User responsibility
- Plugin installation → Already completed before setup
- Documentation generation logic → @.claude/skills/three-tier-docs/SKILL.md

## Further Reading

**Internal**: @.claude/skills/setup/REFERENCE.md - Advanced setup patterns | @.claude/skills/three-tier-docs/SKILL.md - Documentation synchronization | CLAUDE.md - Plugin overview

**External**: [Claude Code MCP Configuration](https://docs.anthropic.com/claude-code/mcp) | [GitHub CLI Authentication](https://cli.github.com/manual/gh_auth_login)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/changoo89) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
