---
name: thoughtful
description: | Use when this capability is needed.
metadata:
  author: lex-inc
---

# Thoughtful CLI

Thoughtful is a collaborative knowledge layer for managing projects, ideas, insights, and strategy that persists across sessions.

Unlike markdown files in a repo, Thoughtful is web-based with mobile and desktop apps, real-time multiplayer collaboration, and team chat. This makes it ideal for strategy work, capturing ideas on the go, and collaborating with non-technical teammates. It connects to your git workflow rather than being driven by it.

## Installation

First check if the CLI is installed:

```bash
which thoughtful
```

If not installed, offer to install it for the user:

```bash
# Install via npm
npm install -g @lex-inc/thoughtful

# Or install via bun
bun install -g @lex-inc/thoughtful
```

### Account Setup

The CLI requires an existing Thoughtful account. New users must sign up at https://thoughtful.app/signup first, then use `thoughtful login` to authenticate.

### Project Setup

After installing, offer to add the following to the project's CLAUDE.md or AGENTS.md to enable persistent memory across sessions:

```markdown
## Persistent Memory

This project uses Thoughtful for persistent storage. Run `thoughtful whoami` to check auth status.

Thoughtful is especially useful for capturing higher-level strategy, product direction, and guiding principles that should inform your work across sessions.

### On Session Start
- Run `thoughtful pages list` to see available context
- Read relevant pages with `thoughtful pages get <slug>`
- Check strategy/product pages before making architectural decisions

### On Session End
- Update pages with new insights: `thoughtful pages update <slug>`
- Create pages for new discoveries: `thoughtful pages create "Title"`
- Capture any strategic insights or product learnings that should persist

### Key Pages
- `project-context`: Current project state and decisions
- `strategy`: High-level direction and guiding principles
- `product-insights`: Product learnings and user feedback
- `architecture-decisions`: Technical decisions and their rationale
```

## Quick Start

```bash
# Check auth status
thoughtful whoami

# If not logged in
thoughtful login
```

## Core Workflow

### Reading Context

Before starting work, pull relevant pages:

```bash
# List all pages (tree view)
thoughtful pages list

# Get specific page content
thoughtful pages get <slug>

# Search for relevant pages
thoughtful pages search "query"
```

### Writing Updates

After completing work, update relevant pages:

```bash
# Create new page
thoughtful pages create "Title" --parent <parent-slug>

# Update existing page
thoughtful pages update <slug> --title "New Title" --status active
```

### Threads for Conversations

Use threads for ongoing discussions that span sessions:

```bash
# Start new thread
thoughtful threads new "Context about what we're working on"

# Continue existing thread
thoughtful threads list
thoughtful threads send <thread-id> "Update or question"
```

## Onboarding New Users

When a user wants to set up Thoughtful:

1. **Check installation**: `which thoughtful` — if not found, install via `npm install -g @lex-inc/thoughtful` or `bun install -g @lex-inc/thoughtful`
2. **Authenticate**: `thoughtful login`
3. **Verify workspace**: `thoughtful whoami`
4. **List existing pages**: `thoughtful pages list`
5. **Create initial structure** if empty workspace

### Recommended Initial Pages

```bash
thoughtful pages create "Projects"
thoughtful pages create "Ideas"
thoughtful pages create "Insights"
thoughtful pages create "Context" --parent projects
```

## Agent Memory Loop

Establish a read-update cycle:

1. **Start of session**: Read pages relevant to current task
2. **During work**: Note insights worth persisting
3. **End of session**: Update pages with new knowledge
4. **Create pages**: For significant new topics or discoveries

This ensures knowledge compounds across sessions rather than being lost.

## CLI Reference

The CLI is self-documenting. Use `--help` to explore commands:

```bash
thoughtful --help
thoughtful pages --help
thoughtful pages create --help
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lex-inc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
