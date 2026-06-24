---
name: ai-prompts-library
description: Search and view system prompts from 40+ AI coding tools (Cursor, Windsurf, Claude Code, Devin, Manus, v0, Lovable, etc). Use for competitive analysis, prompt engineering reference, and understanding how top AI products are built. Use when this capability is needed.
metadata:
  author: 2233admin
---

# AI System Prompts Library

A curated collection of system prompts from 40+ AI coding tools and assistants.

## Setup

On first use, clone the prompt source repository:

```bash
git clone --depth 1 https://github.com/x1xhlol/system-prompts-and-models-of-ai-tools ~/system-prompts-and-models-of-ai-tools
```

## Available Tools & Their Prompts

| Tool | Files | Notes |
|------|-------|-------|
| **Anthropic** | Claude Code, Claude Code 2.0, Claude Sonnet 4.6/4.5, Chrome extension | |
| **Cursor** | Agent v1.0/v1.2/v2.0/2025-09-03, Agent CLI, Chat | Multiple versions |
| **Windsurf** | Prompt Wave 11, Tools Wave 11 | |
| **VSCode Agent** | Main + model-specific (GPT-5, Claude Sonnet 4, Gemini 2.5 Pro, GPT-4.1) | |
| **Devin AI** | Main Prompt, DeepWiki Prompt | |
| **Manus** | Agent loop, Modules, Prompt | |
| **Google** | Antigravity (Fast + Planning), Gemini AI Studio vibe-coder | |
| **Amp** | Claude 4 Sonnet, GPT-5 (YAML) | |
| **Augment Code** | Claude 4 Sonnet, GPT-5 agent prompts | |
| **Lovable** | Agent Prompt + Tools | |
| **v0** | Prompt + Tools | |
| **Replit** | Prompt | |
| **Open Source** | Bolt, Cline, Codex CLI, Gemini CLI, RooCode, Lumo | |
| **Others** | Kiro, Junie, Trae, Same.dev, Leap.new, NotionAI, Perplexity, Warp, Xcode, CodeBuddy, Comet, Orchids, Emergent, Cluely, Poke, Qoder, Traycer, Z.ai, dia | |

## Usage

### List all available tools

```bash
ls ~/system-prompts-and-models-of-ai-tools/ | grep -v -E '^\.|assets|LICENSE|README'
```

### Search for a topic across all prompts

```bash
grep -ril "code review" ~/system-prompts-and-models-of-ai-tools/ --include="*.txt" --include="*.yaml"
```

### View a specific prompt

```bash
cat ~/system-prompts-and-models-of-ai-tools/Cursor\ Prompts/Agent\ Prompt\ 2.0.txt
```

### Compare prompts by size

```bash
find ~/system-prompts-and-models-of-ai-tools/ -type f \( -name "*.txt" -o -name "*.yaml" \) -not -path '*/.git/*' -exec wc -c {} \; | sort -rn | head -20
```

## Common Research Queries

- **"How does X handle file editing?"** - Search for "edit", "patch", "diff"
- **"What tools does X expose?"** - Check `Tools.json` / `tools.json` files
- **"How does X do planning?"** - Search for "plan", "step", "think"
- **"What safety rules does X have?"** - Search for "never", "must not", "forbidden"
- **"How does X handle context/memory?"** - Search for "context", "memory", "history"

## Update

```bash
cd ~/system-prompts-and-models-of-ai-tools && git pull
```

---
> Source: [2233admin/ai-prompts-library](https://github.com/2233admin/ai-prompts-library) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
