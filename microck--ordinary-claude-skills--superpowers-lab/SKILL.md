---
name: superpowers-lab
description: Experimental Claude Code skills extending capabilities with new techniques for interactive CLI tools, tmux automation, and advanced workflows. Use when this capability is needed.
metadata:
  author: microck
---

# Superpowers Lab

Experimental skills that extend Claude Code with innovative techniques and tools under active development. These skills enable new automation patterns and capabilities.

## Included Skills

### using-tmux-for-interactive-commands

Enables Claude Code to control interactive CLI tools through tmux sessions for automation of traditionally manual workflows.

**Supported Tools:**
- Interactive text editors (vim, nano, emacs)
- Terminal UI applications (menuconfig, htop)
- Interactive REPLs (Python, Node, Ruby, etc.)
- Interactive git operations (rebase -i, add -p)
- Any terminal-based tool requiring keyboard navigation

**How It Works:**
- Creates detached tmux sessions
- Sends keystrokes programmatically
- Captures terminal output in real-time
- Enables full automation of interactive workflows

See [skills/using-tmux-for-interactive-commands/SKILL.md](skills/using-tmux-for-interactive-commands/SKILL.md) for detailed documentation.

## Status

Skills in this collection are:
- ✅ Functional and tested
- 🧪 Under active refinement
- 📝 May evolve based on usage and feedback

## Requirements

- tmux must be installed on your system
- Best supported on Linux/macOS
- Requires terminal access for tmux operations

## Getting Started

Explore the subdirectories for individual skill documentation and implementation details. Each skill is self-contained and can be integrated independently.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/microck) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
