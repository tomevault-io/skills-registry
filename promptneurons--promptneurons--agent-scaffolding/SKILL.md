---
name: agent-scaffolding
description: Scaffold a multi-agent workspace with AGENTS.md, .agents/, .claude/, and .gemini/ directories. Use when the user asks to set up a new project for multi-agent development, create an AGENTS.md, or configure a workspace for both Claude Code and Gemini/Antigravity. Use when this capability is needed.
metadata:
  author: promptneurons
---

You are an expert at scaffolding multi-agent workspaces. Your task is to create a workspace structure that enables both Claude Code and Gemini/Antigravity agents to discover skills, follow conventions, and work together in a single repository.

## When to Activate

Activate this skill when the user asks to:
- Set up a new project for AI agent collaboration
- Create an AGENTS.md file
- Configure a workspace for Claude Code and/or Gemini/Antigravity
- Initialize a PromptNeurons-compatible workspace
- Scaffold a multi-agent development environment

## What You Create

### 1. Directory Structure

Create the following directories in the workspace root:

```
.agents/                    # Agent-agnostic source of truth
├── config.json             # Tier, marketplace, installed skills
├── skills/                 # Installed skills (SKILL.md format)
├── workflows/              # Custom slash commands (future)
└── context/                # Project-specific reference docs

.claude/                    # Claude Code mirror
└── plugins/

.gemini/                    # Gemini/Antigravity mirror
├── plugin.json             # Antigravity-native plugin bundle
└── skills/
```

### 2. AGENTS.md

Create `AGENTS.md` at the workspace root. This is the primary bootstrap file read by all agents. Also create `CLAUDE.md` and `GEMINI.md` as copies.

**AGENTS.md must include these sections:**

1. **Project Overview** — project name, tier, marketplace URL, agent runtime
2. **Installed Skills table** — with marker comments for auto-updates:
   ```
   <!-- pn-marketplace:skills-start -->
   | Skill | Source Plugin | Version |
   |-------|-------------|---------|
   <!-- pn-marketplace:skills-end -->
   ```
3. **Agent Discovery Paths** — where each agent finds skills
4. **Workspace Structure** — directory layout reference
5. **Conventions** — source of truth is `.agents/`, skill format, namespacing
6. **Safety Rules** — customize per project (see examples below)
7. **Build & Test** — project-specific commands
8. **Session Completion** — end-of-session checklist

### 3. .agents/config.json

```json
{
  "tier": "<tier>",
  "projectName": "<project-name>",
  "marketplace": "https://github.com/promptneurons/promptneurons-marketplace",
  "installed": {},
  "sync": {
    "targets": ["claude", "gemini"],
    "lastSync": null
  }
}
```

**Tiers:**
- `p100` — Public open source (free, public repos only)
- `h100` — Strategic partner (paid GitHub org member, private repos)
- `h200` — Franchisee (paid, Antigravity IDE required, private repos)

Ask the user which tier they need. Default to `p100` if unsure.

### 4. .gemini/plugin.json

```json
{
  "_generator": "agent-scaffolding-skill",
  "marketplace": "https://github.com/promptneurons/promptneurons-marketplace",
  "tier": "<tier>",
  "plugins": []
}
```

## How to Install Skills After Scaffolding

After creating the scaffold, guide the user to install skills from the PromptNeurons marketplace.

**Method 1 — CLI (if available):**
```bash
npx pn-antigravity-plugin install promptneurons
```

**Method 2 — Manual (always works):**
```bash
git clone https://github.com/promptneurons/promptneurons.git /tmp/promptneurons

# Copy to .agents/ (source of truth):
cp -r /tmp/promptneurons/skills/install-md-generator .agents/skills/promptneurons-skill-install-md-generator

# Mirror to .gemini/ (Antigravity discovery):
cp -r .agents/skills/promptneurons-skill-install-md-generator .gemini/skills/promptneurons-skill-install-md-generator

# Mirror to .claude/ (Claude Code discovery):
mkdir -p .claude/plugins/promptneurons/skills/install-md-generator
cp -r /tmp/promptneurons/skills/install-md-generator/* .claude/plugins/promptneurons/skills/install-md-generator/
cp -r /tmp/promptneurons/.claude-plugin .claude/plugins/promptneurons/.claude-plugin
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/promptneurons/promptneurons.git $env:TEMP\promptneurons

Copy-Item -Recurse -Force "$env:TEMP\promptneurons\skills\install-md-generator" ".agents\skills\promptneurons-skill-install-md-generator"
Copy-Item -Recurse -Force ".agents\skills\promptneurons-skill-install-md-generator" ".gemini\skills\promptneurons-skill-install-md-generator"

New-Item -ItemType Directory -Force -Path ".claude\plugins\promptneurons\skills\install-md-generator"
Copy-Item -Recurse -Force "$env:TEMP\promptneurons\skills\install-md-generator\*" ".claude\plugins\promptneurons\skills\install-md-generator\"
Copy-Item -Recurse -Force "$env:TEMP\promptneurons\.claude-plugin" ".claude\plugins\promptneurons\.claude-plugin"
```

Then update the installed skills table in AGENTS.md and the `.agents/config.json`.

## Safety Rules Examples

### Minimal (P100 — open source projects):
```markdown
## Safety Rules
> Add your project's safety rules here.
```

### Standard (H100/H200 — partner projects):
```markdown
## Safety Rules

### No File Deletion
YOU ARE NEVER ALLOWED TO DELETE A FILE WITHOUT EXPRESS PERMISSION.
Always ask and receive clear, written permission before deleting any file or folder.

### No Destructive Git Operations
`git reset --hard`, `git clean -fd`, `rm -rf` are absolutely forbidden unless
the user explicitly provides the exact command and states they understand the consequences.

### Code Editing Discipline
- Make code changes manually — never run scripts that process/change code files en masse
- Revise existing files in place — never create variations like `mainV2.rs`
```

### Comprehensive (H200 — full multi-agent):
See [AGENTS_KITSAP.md](https://github.com/promptneurons/promptneurons/blob/main/examples/AGENTS_KITSAP.md) for a real-world example with:
- Rule 0 (user override prerogative)
- Irreversible git/filesystem action guards
- Multi-agent coordination (Agent Mail, file reservations)
- Session lifecycle ("landing the plane" workflow)
- Tool-specific integration sections

## Key Principles

1. **`.agents/` is the single source of truth** — skills, config, and context live here
2. **`.claude/` and `.gemini/` are mirrors** — generated from `.agents/`, never edited directly
3. **AGENTS.md is the bootstrap** — agents read this first to understand the workspace
4. **Skills use SKILL.md format** — YAML frontmatter (`name`, `description`) + markdown instructions
5. **Namespacing:** installed skills use `promptneurons-skill-<name>` prefix
6. **CLAUDE.md and GEMINI.md are copies** of AGENTS.md — keep them in sync

## Execution Steps

When the user triggers this skill:

1. Ask for: project name, tier (p100/h100/h200), and any custom conventions
2. Create all directories listed above
3. Generate AGENTS.md with the user's project info filled in
4. Copy AGENTS.md to CLAUDE.md and GEMINI.md
5. Create `.agents/config.json` with tier and marketplace URL
6. Create `.gemini/plugin.json` bundle manifest
7. Ask if they want to install skills from the marketplace now
8. If yes, execute the manual install steps above
9. Report what was created and next steps

---
> Source: [promptneurons/promptneurons](https://github.com/promptneurons/promptneurons) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
