---
name: skill-installer
description: Install and manage AI agent skills from Git repositories using the `npx skills add` command. Use this skill when users want to install skills from GitHub repositories or other Git sources, either installing a specific skill or all skills from a repository. Use when this capability is needed.
metadata:
  author: doitian
---

# Skill Installer

## Overview

This skill enables installing AI agent skills from Git repositories using the `npx skills add` command. It provides guidance and automation for installing individual skills or entire skill repositories.

## Installing Skills

### Basic Usage

**IMPORTANT:** Always use the `-y` flag to skip confirmation prompts, and always install at project-level (never use `-g`).

To install a specific skill from a Git repository:

```bash
npx skills add GIT_REPO --skill SKILL_NAME -y
```

**Example:**
```bash
npx skills add https://github.com/doitian/skills-repo --skill skill-creator -y
npx skills add doitian/skills-repo -s skill-installer -y
```

To install all skills from a repository:

```bash
npx skills add GIT_REPO -y
```

**Example:**
```bash
npx skills add https://github.com/doitian/skills-repo -y
```

### Command Options

The `npx skills add` command supports several options:

- `-s, --skill <skills...>` - Specify skill names to install (skip selection prompt)
- `-y, --yes` - **REQUIRED**: Skip confirmation prompts (always use this)
- `-a, --agent <agents...>` - Specify agents to install to (opencode, claude-code, cline, etc.)
- `-l, --list` - List available skills in the repository without installing

**Note:** Never use `-g` or `--global` flags. Always install at project-level.

### Supported Repository Formats

The command accepts various Git repository formats:

- Full HTTPS URLs: `https://github.com/owner/repo`
- Short GitHub format: `owner/repo` (GitHub is assumed)
- Local paths: `./path/to/skill`
- Direct path to skill file

### Common Workflows

**1. Installing a single skill:**

When a user wants to add just one skill from a repository:

```bash
npx skills add <git-repo> --skill <skill-name> -y
# or
npx skills add <git-repo> -s <skill-name> -y
```

**2. Installing all skills:**

When a user wants to add all available skills from a repository:

```bash
npx skills add <git-repo> -y
```

**3. Listing available skills:**

Before installation, users can check what skills are available:

```bash
npx skills add <git-repo> --list
```

**Note:** Always use the `-y` flag when installing to skip confirmation prompts. Never use `-g` or `--global` - always install at project-level.

## Installation Script

For automated or batch installation, use the provided `install_skill.sh` script:

```bash
scripts/install_skill.sh <git-repo> [--skill <skill-name>]
```

The script handles:
- Repository URL validation
- Skill existence verification
- Error handling and user feedback
- Support for various Git URL formats

## Troubleshooting

**Skill not found:**
- Verify the skill name matches the directory name in the repository's `skills/` folder
- Use `npx skills add <repo> --list` to see available skills
- Check the repository README for the list of available skills

**Installation fails:**
- Ensure you have Node.js and npm installed
- Check your internet connection
- Verify the Git repository URL is correct and accessible

**Skills CLI not working:**
- Update to the latest version: `npm install -g skills`
- Or use npx to ensure the latest version: `npx skills@latest add ...`

## Advanced Usage

For detailed CLI reference, advanced options, and troubleshooting, see [references/cli_reference.md](references/cli_reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doitian) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
