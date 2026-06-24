---
name: gitlog
description: Git log skill for analyzing commit history, generating git status reports, and working with git repositories. Use this when users need to view commit history, check repository status, analyze changes, or generate git-related reports. Use when this capability is needed.
metadata:
  author: eclipse-oniro-mirrors
---

# Git Log Skill

```
╔══════════════════════════════════════════════════════════════╗
║  DEBUG: Git Log Skill v1.1.0                                ║
║  DEBUG: Author: Created by user                             ║
║  DEBUG: Created: 2026-01-20                                 ║
║  DEBUG: Status: ✓ Loaded and ready                          ║
║  DEBUG: Location: .claude/skills/gitlog/SKILL.md            ║
╚══════════════════════════════════════════════════════════════╝
```

> **Author**: Created by user  
> **Created**: 2026-01-20  
> **Version**: 1.1.0  
> **Proof of Ownership**: This skill was created and customized for VK-GL-CTS project

This skill provides capabilities for working with Git repositories, including viewing commit history, checking repository status, and generating git-related reports.

## Using Git Log Skill in Conversation

**The AI assistant automatically uses this skill when you ask git-related questions!**

Simply ask in natural language, and the assistant will use this skill to help you:

### Conversation Examples:

**Basic queries:**
- "Show git log" → Assistant uses skill to show recent commits
- "Show git status" → Assistant uses skill to check repository status
- "查看最近的提交" → Assistant uses skill to show recent commits

**Specific requests:**
- "Show last 10 commits" → Uses `log 10` command
- "Show git log with statistics" → Uses `log-stat` command
- "Show commits for CMakeLists.txt" → Uses `log-file` command
- "Show commits between v1.4.3 and v1.4.4" → Uses `log-range` command
- "Generate git report for CTS submission" → Uses `report` command
- "查看本项目有多少分支" → Uses `branches` command
- "列出所有本地分支" → Uses `branches --local` command
- "查看远程分支分类统计" → Uses `branches --remote` command

**Advanced queries:**
- "查看两个标签之间的提交" → Uses `log-range` command
- "生成 CTS 提交需要的 git log 文件" → Uses `report` command with `--first-parent`
- "查看某个文件的提交历史" → Uses `log-file` command
- "统计远程分支有多少 weekly / release / feature" → Uses `branches --remote` command
- "导出某目录下所有提交到 CSV" / "dir-csv" → Uses **`dir-csv`** 命令，CSV 默认落在 **`.claude/skills/gitlog/`**

### How It Works:

1. **You ask a question** in natural language (English or Chinese)
2. **AI assistant recognizes** it's a git-related task
3. **AI assistant uses** the gitlog skill knowledge to execute the appropriate command
4. **Results are shown** with debug information proving it's your custom skill

**No need to remember commands!** Just ask naturally, and the assistant will handle it.

## Quick Start - Using the Executable Script

You can use the skill with specific commands and operations:

```bash
# Basic usage
.claude/skills/gitlog/gitlog.sh <command> [arguments]

# Examples:
.claude/skills/gitlog/gitlog.sh status              # Show git status
.claude/skills/gitlog/gitlog.sh log 5               # Show last 5 commits
.claude/skills/gitlog/gitlog.sh log-oneline 20      # Show last 20 commits (one-line)
.claude/skills/gitlog/gitlog.sh log-stat 10         # Show last 10 commits with stats
.claude/skills/gitlog/gitlog.sh log-patch 5         # Show last 5 commits with full diff
.claude/skills/gitlog/gitlog.sh log-file CMakeLists.txt  # Show log for specific file
.claude/skills/gitlog/gitlog.sh report v1.4.4.0    # Generate git reports
.claude/skills/gitlog/gitlog.sh help                # Show help message
```

**Available Commands:**
- `status` - Show git status
- `log [n]` - Show last n commits (default: 10) in one-line format
- `log-oneline [n]` - Show last n commits in one-line format
- `log-stat [n]` - Show last n commits with file statistics
- `log-patch [n]` - Show last n commits with full patch/diff
- `log-file <file>` - Show git log for specific file
- `log-range <from>..<to>` - Show commits between two references (e.g., `tag1..tag2`)
- `log-first-parent <range>` - Show commits with --first-parent option (e.g., `tag^..HEAD`)
- `report [tag]` - Generate git-status.txt and git-log.txt files
- `branches [--all|--local|--remote]` - List and count branches with categorized statistics
- `dir-csv <dir> [--remote origin] [--out file.csv]` - Export commit history for that path to CSV (default under `.claude/skills/gitlog/`)
- `commit [message] [--no-sign]` - Auto commit (and push) changed files; **default adds `-s` / Signed-off-by** (see section below)
- `help` - Show help message

**Examples for range queries:**
```bash
# Show commits between two tags
.claude/skills/gitlog/gitlog.sh log-range v1.4.3..v1.4.4

# Show commits with --first-parent (for CTS submissions)
.claude/skills/gitlog/gitlog.sh log-first-parent v1.4.4.0^..HEAD

# Equivalent to: git log --first-parent v1.4.4.0^..HEAD
```

**Examples for branch queries:**
```bash
# List all branches (local + remote) with category statistics
python3 .claude/skills/gitlog/gitlog.py branches

# List local branches only
python3 .claude/skills/gitlog/gitlog.py branches --local

# List remote branches only (with category breakdown)
python3 .claude/skills/gitlog/gitlog.py branches --remote
```

## 在本仓库代用户提交（Agent / Cursor 必读）

本技能 **`commit`** 子命令在代码中**默认等价于 `git commit -s`**（自动追加 **Signed-off-by**，与 `user.name` / `user.email` 一致）。**代用户执行提交时必须先读本节**，勿裸用不带签名的 `git commit`。

| 场景 | 做法 |
|------|------|
| **自动处理工作区全部改动并提交（含行数拆分逻辑），随后可能 push** | 在仓库根：`python src/skills/gitlog/gitlog.py commit "提交说明"`。不需要签名时用：`… commit --no-sign "说明"`。 |
| **只提交已 `git add` 的部分文件** | 先 `git add <路径…>`，再：**`git commit -s -m "说明"`**（**禁止省略 `-s`**）。与 `--signoff` 等价。 |

路径以 **`napi_generator` 仓库根**为准：`src/skills/gitlog/gitlog.py`。

## Common Git Commands

### Viewing Branches

```bash
# List all local branches
git branch

# List all local and remote branches
git branch -a

# List remote branches only
git branch -r

# Using gitlog skill (with category statistics):
python3 .claude/skills/gitlog/gitlog.py branches
python3 .claude/skills/gitlog/gitlog.py branches --local
python3 .claude/skills/gitlog/gitlog.py branches --remote
```

### Viewing Commit History

```bash
# Basic git log
git log

# One-line format
git log --oneline

# Show last N commits
git log -n 10

# Show commits with file changes
git log --stat

# Show commits with full diff
git log -p

# Show commits for specific file
git log -- <file_path>

# Show commits between tags/branches
git log <tag1>..<tag2>
git log --first-parent <release tag>^..HEAD

# Using gitlog skill for range queries:
.claude/skills/gitlog/gitlog.sh log-range <tag1>..<tag2>
.claude/skills/gitlog/gitlog.sh log-first-parent <release tag>^..HEAD
```

### Repository Status

```bash
# Check repository status
git status

# Short status format
git status -s

# Show untracked files
git status -u
```

### Generating Reports

For CTS submissions, you may need to generate git status and git log files:

```bash
# Generate git status file
git status > git-status.txt

# Generate git log file (for submissions)
git log --first-parent <release tag>^..HEAD > git-log.txt

# Using gitlog skill to generate reports:
.claude/skills/gitlog/gitlog.sh report <release tag>
# This automatically generates git-status.txt and git-log.txt

# Generate patches
git format-patch -o <output_dir> <release tag>..HEAD
```

## Usage Guidelines

- Use `git log` to analyze commit history and understand changes
- Use `git status` to check repository state before builds or submissions
- For CTS builds, ensure repository is clean (no uncommitted changes)
- When generating submission packages, include both git-status.txt and git-log.txt files
- Use `git cherry-pick -x` when applying bug fixes to include original commit hash

## Integration with CTS Workflow

According to CTS requirements:
- CTS builds must be done from clean git repository
- Capture `git status` and `git log` output for submission packages
- Use `git format-patch` to provide changes as part of submission package
- Commit messages must include clear descriptions of changes

## Debugging and Verification

### How to Verify Skill is Loaded

When you invoke this skill using `npx openskills read gitlog`, you should see:

```
DEBUG: Git Log Skill v1.0.0 loaded
DEBUG: Author: Created by user
DEBUG: Created: 2026-01-20
DEBUG: Skill is ready for use
```

### Debug Mode Commands

To verify the skill is working correctly, you can run these debug commands:

```bash
# Verify skill exists
ls -la .claude/skills/gitlog/SKILL.md

# Check skill content
cat .claude/skills/gitlog/SKILL.md | head -10

# Test skill loading
npx openskills read gitlog | grep -i "debug\|author\|version"
```

### Debug Output Format

When this skill is used, it will output debug information in the following format:
- **Skill Name**: gitlog
- **Version**: 1.1.0
- **Author**: Created by user
- **Status**: Active and ready

---

## Skill Metadata

**Author**: Created by user  
**Creation Date**: 2026-01-20  
**Version**: 1.1.0  
**Location**: `.claude/skills/gitlog/SKILL.md`  
**Original Source**: Custom skill created for VK-GL-CTS project workflow

This skill was created to provide git log analysis capabilities specifically for the VK-GL-CTS repository management and CTS submission workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eclipse-oniro-mirrors) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
