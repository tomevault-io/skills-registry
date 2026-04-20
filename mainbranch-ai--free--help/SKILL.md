---
name: help
description: Answer questions about Main Branch and Claude Code. Use when: user asks how/what/why questions, is confused about two-repo model or skills, encounters errors, says help or stuck, asks about workflow, is a beginner, or wants to know what to do next. Use when this capability is needed.
metadata:
  author: mainbranch-ai
---

# Help

Answer questions, troubleshoot issues, explain philosophy, suggest next steps.

---

## Workflow

1. **Triage** -- Parse user's question/brain-dump
2. **Detect business type** -- Check `reference/core/*.md` (Skool? Ecommerce?)
3. **Load reference** -- Find topic in references/ table below
4. **Answer** -- Explain "why" not just "what"
5. **Route** -- End with next skill or action

---

## Topic Router

| Keywords | Reference |
|----------|-----------|
| Terminal, drag files, cd, folder | [terminal-basics.md](references/terminal-basics.md) |
| Two repos, engine, data model | [two-repos.md](references/two-repos.md) |
| Philosophy, why, compound, passive memory | [philosophy.md](references/philosophy.md) |
| /think, research, decide, codify | [the-think-cycle.md](references/the-think-cycle.md) |
| Task tracking, where left off, focus | [task-tracking-options.md](references/task-tracking-options.md) |
| Error, command not found, MCP, Apify setup | [troubleshooting.md](references/troubleshooting.md) |
| Getting started, setup | Route to `/setup` or `/start` |
| Which skill, when to use | [skills-guide.md](references/skills-guide.md) |
| Migrate from GPT, ChatGPT | [gpt-migration.md](references/gpt-migration.md) |
| Better outputs, quality, what next | [making-outputs-better.md](references/making-outputs-better.md) |
| Subagents, parallel, agents, context window, tokens | [working-with-agents.md](references/working-with-agents.md) |
| Ads, organic, reels, video scripts, VSL, landing page, site, wiki, compliance, lenses | **Premium feature** (see Premium Response below) |

---

## Premium Response

When someone asks about output generation skills, respond:

> "Output generation skills (ads, organic content, video scripts, landing pages, compliance review) are available with Main Branch Premium. The free version includes `/think`, `/start`, `/setup`, `/pull`, and `/help` -- everything you need to build your reference files and make decisions. When you're ready for AI-generated content, learn more at skool.com/main-branch"

---

## Principles

- **Explain "why"** -- Not just steps
- **End with action** -- Suggest next skill (`/think`, `/setup`, `/start`)
- **Beginner-friendly** -- Many never used Terminal

---

## Quick Answers

| Question | Answer |
|----------|--------|
| Start Claude in a folder? | `cd ~/mb-free && claude` -- Claude sees files in that folder |
| When use slash commands? | For structured tasks: `/start`, `/think`, `/setup` |
| Drag files in? | Drag from Finder into Terminal, path appears |
| Voice input? | [Wispr Flow](https://ref.wisprflow.ai/main) (affiliate link) |
| What are subagents? | Claude can spawn parallel agents to research or review simultaneously. You'll see it happen automatically in `/think` (multi-source research). Each agent gets its own context window so your main conversation stays clean. |
| How do I manage context/tokens? | When context gets heavy, break work into parallel subagents. Heavy research (transcripts, mining) runs in subagents so raw content stays out of your main conversation. Synthesized summaries come back instead. Re-invoke `/think` after compaction to reload context. |
| What about ads, organic, VSL? | Output generation skills are available with Main Branch Premium. Learn more at skool.com/main-branch |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mainbranch-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
