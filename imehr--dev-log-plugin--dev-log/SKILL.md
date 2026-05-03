---
name: dev-log
description: Automatically log development sessions - captures tools, decisions, accounts, and progress from conversation context Use when this capability is needed.
metadata:
  author: imehr
---

# Dev-Log - Automatic Development Session Logging

Automatically captures development work from conversation context. One file per project with timestamped entries. No questions asked - just run `/dev-log` and it extracts everything.

## When to Use

- After substantive work (20+ minutes of development)
- When installing/configuring new tools or SDKs
- When creating accounts for services
- When making key architectural decisions
- Before switching projects (capture context while fresh)
- Before ending a session

## When NOT to Use

- Reading documentation (no work produced)
- Quick fixes under 10 minutes
- Exploratory sessions with no concrete outcome

## Storage Location

**Root**: `~/dev-log/` (configurable)

### First Run Setup

When you first run `/dev-log`, if the directory doesn't exist, you'll be asked:

**"Where would you like to store development logs?"**

Options:
- **Default location** (`~/dev-log/`) - Recommended for personal use across all projects
- **Current project** (`./.dev-log/`) - Keep logs within project directory
- **Custom path** - Specify your own location

Your choice is saved to `.claude/dev-log.local.md` in the current project.

### Directory Structure

```
dev-log/
├── projects/
│   ├── clawdbot.md      # All clawdbot work
│   ├── dev-log.md       # This skill's development
│   └── <project>.md     # One file per project
├── drafts/              # Content creation outputs
└── index.json           # Project metadata
```

## Quick Reference

| Command | Action |
|---------|--------|
| `/dev-log` | Auto-capture session to detected project |
| `/dev-log <project>` | Auto-capture to specific project |
| `/dev-log show` | View current project entries |
| `/dev-log show <project>` | View specific project entries |
| `/dev-log list` | List all projects |
| `/dev-log interview` | 12-question content capture (for publishing) |
| `/dev-log content` | Generate content from existing logs |
| `/dev-log drafts` | List content drafts |
| `/dev-log help` | Show detailed help |

## Default Mode (Auto-Capture)

Running `/dev-log` automatically extracts from conversation:

- **Summary**: What was accomplished
- **Decisions Made**: Key choices with rationale
- **Tools & SDKs**: CLIs, libraries, services used
- **Accounts & Credentials**: Signups, API keys created
- **Configuration**: Files created/modified
- **Key Commands**: Significant bash commands run
- **Problems Solved**: Issues and resolutions
- **Next Steps**: Pending work identified
- **Status**: Working/In Progress/Blocked/Exploratory

**No questions asked.** Just analyzes the conversation and logs.

## Interview Mode (Content Creation Only)

Use `/dev-log interview` ONLY when you want to create publishable content.

Generates:
- Twitter/X thread (7-12 tweets)
- LinkedIn post (~2000 chars)
- Screenshot script

## Project Detection

Auto-detects from:
1. Git remote URL
2. Git repository root
3. Current working directory

Override: `/dev-log my-project-name`

## Example Entry

```markdown
## 2026-01-15 18:26 AEDT

### Summary
Set up Clawdbot with Tailscale Funnel for Gmail webhooks...

### Decisions Made
- **Tailscale over ngrok**: Persistent URL, no signup...

### Tools & SDKs
| Tool | Purpose | Notes |
|------|---------|-------|
| Tailscale | Public HTTPS | Funnel enabled |

### Next Steps
- [ ] Auth ElevenLabs CLI
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imehr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
