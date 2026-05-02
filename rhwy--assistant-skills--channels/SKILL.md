---
name: channels
description: Git-like conversation channel management for multi-agent workspaces. Use when organizing work into isolated streams, branching topics, committing progress checkpoints, or merging completed work. Supports multiple agents sharing channels via cloud sync. Use when this capability is needed.
metadata:
  author: rhwy
---

# Channels Skill

Manage conversation channels like git branches — branch, commit, merge.

## Initial Setup

Before first use, run the setup script:

```bash
./scripts/channel-setup.sh
```

This will prompt for:
1. **ASSISTANTS_HOME** — Path to cloud-synced folder (Dropbox, iCloud, etc.)
2. **Agent name** — Name for this agent instance
3. **Docs format** — How to reference documents (full paths, relative paths, or custom)

Configuration is saved to `{ASSISTANTS_HOME}/config.json`.

## Structure

```
{ASSISTANTS_HOME}/
├── config.json             # Shared configuration
├── channels/
│   ├── _registry.json      # Channel index
│   └── {channel-name}/
│       ├── channel.json    # Metadata
│       ├── context.md      # Working context
│       └── commits/        # Checkpoints (UTC timestamps)
├── agents/{name}.json      # Agent state
├── transcripts/            # Full logs
├── archives/               # Merged archives
└── daily/                  # Cross-cutting notes
```

## Operations

### List Channels

```bash
./scripts/channel-list.sh
```

### Branch

Create child channel from parent:

```bash
./scripts/channel-branch.sh <parent-name> <child-name> "<description>"
```

### Checkout

Switch agent's active channel:

```bash
./scripts/channel-checkout.sh <agent-name> <channel-name>
```

### Commit

Save checkpoint with summary of work since last commit:

```bash
./scripts/channel-commit.sh <channel-name> "<summary>"
```

**Commit format:**
```markdown
# Commit: {UTC-timestamp}

## Context
Brief description of the channel's purpose

## Work Done
- Item 1
- Item 2

## Outputs
- file1.md — description
- file2.json — description

## Next
- Pending items
```

### Merge

Archive completed work and send summary to parent:

```bash
./scripts/channel-merge.sh <child-name>
```

## channel.json Schema

```json
{
  "name": "#channel-name",
  "description": "What this channel is for",
  "type": "branch",
  "parent": "#parent-name",
  "status": "active|paused|merged",
  "created": "2026-01-30T00:00:00Z",
  "workdir": "/path/to/working/directory",
  "docs": ["/path/to/doc.md", "relative/path.md"],
  "lastCommit": "2026-01-30T00-00-00Z",
  "activeAgents": ["agent1"],
  "mergedAt": null
}
```

**Note:** `docs` format depends on your setup configuration. Use whatever path format makes sense for your environment.

## Agent State

Each agent maintains `agents/{name}.json`:

```json
{
  "name": "agent1",
  "host": "machine-name",
  "currentChannel": "#general",
  "lastActive": "2026-01-30T00:00:00Z",
  "capabilities": ["local-access", "messaging"]
}
```

## Best Practices

1. **Commit often** — Before pausing work, before context fills up
2. **Branch for focus** — New topic? Branch it, merge when done
3. **Keep context.md current** — It's the live working state
4. **UTC timestamps everywhere** — Universal, no timezone confusion
5. **Human-readable** — All files browsable in any file manager/editor

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rhwy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
