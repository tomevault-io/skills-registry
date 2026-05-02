---
name: copy-skill
description: Copy Claude Code skills from GitHub repositories into local .claude/skills directory. Use when the user wants to install, download, or copy a skill from GitHub, or mentions a GitHub URL to a skill they want to add. Use when this capability is needed.
metadata:
  author: khang859
---

# Copy Skill

Downloads and installs Claude Code skills from GitHub repositories to your local `.claude/skills` directory.

## Quick Start

```bash
node scripts/copy-skill.js <github-url> [--target <path>]
```

## Instructions

### Step 1: Parse the GitHub URL

Accept URLs in these formats:
- `https://github.com/owner/repo/tree/branch/path/to/skill`
- `https://github.com/owner/repo/tree/main/skills/skill-name`

### Step 2: Run the copy script

```bash
# Copy to project .claude/skills (default)
node scripts/copy-skill.js https://github.com/user/repo/tree/main/skills/my-skill

# Copy to user home ~/.claude/skills
node scripts/copy-skill.js https://github.com/user/repo/tree/main/skills/my-skill --target ~/.claude/skills
```

### Step 3: Verify installation

The script will:
1. Fetch the directory listing from GitHub API
2. Recursively download all files (SKILL.md, scripts/, references/, etc.)
3. Create the local directory structure
4. Write all files preserving the structure

## Usage Examples

**Copy a skill from a public repository:**
```bash
node scripts/copy-skill.js https://github.com/owner/repo/tree/main/skills/my-skill
```

**Copy to a custom location:**
```bash
node scripts/copy-skill.js https://github.com/user/skills/tree/main/my-skill --target /path/to/.claude/skills
```

**Copy to home directory skills:**
```bash
node scripts/copy-skill.js https://github.com/user/repo/tree/main/skill-name --target ~/.claude/skills
```

## Script Options

| Option | Description | Default |
|--------|-------------|---------|
| `--target`, `-t` | Target directory for skill installation | `.claude/skills` |
| `--name`, `-n` | Override skill directory name | Extracted from URL |
| `--verbose`, `-v` | Enable verbose output | `false` |

## Requirements

- Node.js 18+ (for native fetch support)
- Internet connection to access GitHub API

## How It Works

1. **URL Parsing**: Extracts owner, repo, branch, and path from GitHub URL
2. **API Fetching**: Uses GitHub Contents API to list all files recursively
3. **Content Download**: Fetches raw content for each file
4. **Directory Creation**: Creates local directory structure mirroring the source
5. **File Writing**: Writes all files to the target location

## Error Handling

The script handles:
- Invalid GitHub URLs
- Non-existent repositories or paths
- Network failures with retry logic
- Rate limiting (suggests using GITHUB_TOKEN)

## Troubleshooting

**Rate limit exceeded:**
```bash
export GITHUB_TOKEN=your_token_here
node scripts/copy-skill.js <url>
```

**Permission denied:**
- Check write permissions on target directory
- Try with sudo or change target path

**Skill not found after install:**
- Restart Claude Code to reload skills
- Verify SKILL.md exists in the installed directory

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khang859) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
