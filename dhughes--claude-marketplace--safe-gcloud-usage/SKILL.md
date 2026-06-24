---
name: safe-gcloud-usage
description: This skill should be used when the user asks to "run a gcloud command", "use gcloud", "access Google Cloud", "list GCP projects", "check gcloud config", or any task involving the Google Cloud CLI. Also use when Claude needs to suggest patterns for the gcloud allowlist or explain why a gcloud command was blocked. Use this skill immediately when you see "Direct gcloud commands are blocked" or "Use the safe-gcloud wrapper instead" in an error message. Use when this capability is needed.
metadata:
  author: dhughes
---

# Using gcloud Safely with safe-gcloud

## Overview

Direct `gcloud` commands are blocked in this environment. All Google Cloud CLI operations must go through the `safe-gcloud` wrapper script, which enforces a project-specific allowlist of permitted commands.

## How to Run gcloud Commands

Execute gcloud commands through the safe-gcloud wrapper script:

```bash
bash ${CLAUDE_PLUGIN_ROOT}/skills/safe-gcloud-usage/scripts/safe-gcloud.sh <command> [args...]
```

Examples:
```bash
bash ${CLAUDE_PLUGIN_ROOT}/skills/safe-gcloud-usage/scripts/safe-gcloud.sh projects list --format=json
bash ${CLAUDE_PLUGIN_ROOT}/skills/safe-gcloud-usage/scripts/safe-gcloud.sh config get-value project
bash ${CLAUDE_PLUGIN_ROOT}/skills/safe-gcloud-usage/scripts/safe-gcloud.sh auth list
```

The wrapper passes all arguments, flags, and piped input directly to `gcloud` for permitted commands.

## Allowlist Configuration

Each project defines permitted commands in `.claude/gcloud-allowlist.json`. This file contains a JSON array of command patterns.

### Pattern Syntax

| Pattern | Matches | Does Not Match |
|---------|---------|----------------|
| `projects list` | Exactly `gcloud projects list` | `gcloud projects list --format=json` |
| `projects list:*` | `gcloud projects list` with any args/flags | `gcloud projects describe` |
| `projects:*` | Any `gcloud projects` subcommand | `gcloud compute instances list` |

### Example Allowlist

```json
[
  "projects list:*",
  "config get-value:*",
  "auth list",
  "compute instances list:*",
  "compute instances describe:*"
]
```

## Critical Restrictions

**NEVER attempt to edit `.claude/gcloud-allowlist.json`**. This file is under user control only. When a command is blocked due to missing permissions:

1. Inform the user which command was blocked
2. Suggest the pattern they could add to permit it
3. Wait for the user to update the allowlist themselves

## When Commands Are Blocked

If safe-gcloud blocks a command, it provides:
- The exact command that was attempted
- An error explaining the command is not in the allowlist
- A suggested pattern to add

Example blocked output:
```
ERROR: Command not permitted by allowlist.

Attempted command: gcloud compute instances list --zone=us-central1-a

The allowlist at .claude/gcloud-allowlist.json does not include a pattern that permits this command.

To allow this command, add an appropriate pattern to the allowlist.
For example, to allow this specific command with any flags:
  "compute instances list:*"
```

## Suggesting Patterns to Users

When the user asks what pattern to add, analyze the command and suggest the appropriate pattern:

- For a specific command with flags: `"<command> <subcommand>:*"`
- For all subcommands of a service: `"<service>:*"`
- For exact command only (no flags): `"<command> <subcommand>"`

Example suggestions:
- User wants `gcloud run deploy`: suggest `"run deploy:*"`
- User wants all compute commands: suggest `"compute:*"`
- User wants only `gcloud auth list` with no flags: suggest `"auth list"`

## Missing Allowlist File

If `.claude/gcloud-allowlist.json` does not exist, all gcloud commands are blocked. Inform the user they need to create this file with their desired patterns.

## Dependencies

The safe-gcloud wrapper requires:
- `gcloud` CLI installed and configured

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dhughes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
