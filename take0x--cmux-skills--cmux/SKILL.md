---
name: cmux
description: Use this skill when the user asks about cmux, mentions cmux terminal, asks about cmux configuration, keyboard shortcuts, custom commands, notifications, browser automation, the cmux API, cmux split panes, cmux vertical tabs, cmux workspaces, or Claude Code agent teams in cmux. Also use when the user asks about a native macOS terminal for AI coding agents built on Ghostty, or references cmux.com documentation.
metadata:
  author: take0x
---

# cmux Documentation Skill

## Overview

cmux is a native macOS terminal application built on Ghostty (libghostty),
designed for developers running multiple AI coding agents simultaneously.
Key features include vertical tabs, notification rings, split panes, an
embedded browser, and a Unix socket API for automation. It is
GPU-accelerated, built with Swift and AppKit (no Electron), and is free
and open-source.

- **GitHub**: https://github.com/manaflow-ai/cmux
- **Website**: https://cmux.com

## Documentation URL Reference

When answering questions about cmux, fetch the relevant documentation page
from cmux.com using WebFetch. Select the most relevant URL based on the
user's question:

| Topic | URL |
|---|---|
| Installation and setup | https://cmux.com/docs/getting-started |
| Core concepts (windows, workspaces, panes, surfaces) | https://cmux.com/docs/concepts |
| Configuration (Ghostty config) | https://cmux.com/docs/configuration |
| Keyboard shortcuts | https://cmux.com/docs/keyboard-shortcuts |
| Custom commands and workspace layouts | https://cmux.com/docs/custom-commands |
| CLI and Unix socket API | https://cmux.com/docs/api |
| Notifications and alerts | https://cmux.com/docs/notifications |
| Browser automation | https://cmux.com/docs/browser-automation |
| Claude Code agent teams | https://cmux.com/docs/claude-code-teams |
| Changelog and version history | https://cmux.com/docs/changelog |
| Blog posts and news | https://cmux.com/blog |
| General overview | https://cmux.com |

## Source Selection

Classify the user's question, then pick the information source:

| Question type | Examples | Source |
|---|---|---|
| CLI usage, subcommands, flags, syntax | "how do I list panes?", "cmux browser commands", "what flags does new-workspace take?" | **Bash first**: run `cmux -h` or `cmux <subcommand> --help` |
| API / scripting / automation | "how to automate cmux", "send keys to a pane via script" | **Bash first**, then WebFetch the API page if deeper context needed |
| Concepts, config, GUI, shortcuts, teams | "keyboard shortcuts", "how workspaces work", "notification settings", "claude code teams" | **WebFetch** from the URL table above |
| Ambiguous or broad | "tell me about cmux panes", "how do splits work?" | **Both**: Bash for CLI details + WebFetch for conceptual context |

## How to Answer

1. Classify the user's question using the Source Selection table above.

2. For **Bash-first** questions:
   - Run `cmux -h` to get the full command list, or
     `cmux <subcommand> --help` for details on a specific subcommand.
   - The CLI help output is authoritative for syntax, flags, and available
     subcommands -- it reflects the user's installed version.
   - If the help output fully answers the question, use it directly.
   - If the user needs more context (examples, workflows, rationale),
     supplement by fetching the relevant cmux.com page with WebFetch.

3. For **WebFetch** questions:
   - Select the most relevant URL from the Documentation URL Reference table.
   - Use WebFetch with a targeted extraction prompt.
   - If the first page does not answer, try a different page from the table.

4. For **both-sources** questions:
   - Start with Bash to get the concrete CLI details.
   - Then fetch the relevant cmux.com page for conceptual context.
   - Merge both into a unified answer.

5. For simple questions like "what is cmux?", answer from the Overview above
   without fetching, unless the user asks for more details.

## Response Format

- Provide concise, actionable answers
- Include relevant code snippets, configuration examples, or keyboard
  shortcuts when applicable
- Link to the source documentation page so the user can read more
- If the documentation does not cover the user's question, state this clearly
  rather than speculating

---
> Source: [take0x/cmux-skills](https://github.com/take0x/cmux-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
