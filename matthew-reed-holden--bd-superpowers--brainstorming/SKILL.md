---
name: brainstorming
description: You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation. Use when this capability is needed.
metadata:
  author: matthew-reed-holden
---

# Brainstorming Ideas Into Designs

## Overview

Help turn ideas into fully formed designs and specs through natural collaborative dialogue.

Start by understanding the current project context, then ask questions one at a time to refine the idea. Once you understand what you're building, present the design in small sections (200-300 words), checking after each section whether it looks right so far.

## The Process

**Understanding the idea:**
- Check out the current project state first (files, docs, recent commits)
- Ask questions one at a time to refine the idea
- Prefer multiple choice questions when possible, but open-ended is fine too
- Only one question per message - if a topic needs more exploration, break it into multiple questions
- Focus on understanding: purpose, constraints, success criteria

**Exploring approaches:**
- Propose 2-3 different approaches with trade-offs
- Present options conversationally with your recommendation and reasoning
- Lead with your recommended option and explain why

**Presenting the design:**
- Once you believe you understand what you're building, present the design
- Break it into sections of 200-300 words
- Ask after each section whether it looks right so far
- Cover: architecture, components, data flow, error handling, testing
- Be ready to go back and clarify if something doesn't make sense

## After the Design

**Documentation:**
- Create a beads epic to track the design:
  ```bash
  source skills/lib/beads-helper.sh
  epic_id=$(beads_create_design_epic "Feature Name" "$(cat <<'EOF'
  [Full design content here - include all sections discussed]
  
  Example:
  ## Architecture
  - Component A handles X
  - Component B handles Y
  
  ## Data Flow
  [etc...]
  EOF
  )")
  echo "Created design epic: $epic_id"
  ```
- Write markdown reference file at `docs/plans/$(date +%Y-%m-%d)-<topic>-design.md`:
  - Include link to epic at top: `**Beads Epic:** bd-abc123` (use the actual epic ID)
  - Include view command: `bd show bd-abc123`
  - Include the full design content for easy reading
- Commit the markdown reference file (beads epic is auto-saved in `.beads/`)
- The epic ID will be used when creating the implementation plan

**Implementation (if continuing):**
- Ask: "Ready to set up for implementation?"
- Use superpowers:using-git-worktrees to create isolated workspace
- Use superpowers:writing-plans to create detailed implementation plan

## Key Principles

- **One question at a time** - Don't overwhelm with multiple questions
- **Multiple choice preferred** - Easier to answer than open-ended when possible
- **YAGNI ruthlessly** - Remove unnecessary features from all designs
- **Explore alternatives** - Always propose 2-3 approaches before settling
- **Incremental validation** - Present design in sections, validate each
- **Be flexible** - Go back and clarify when something doesn't make sense

## Beads Integration

This skill creates design epics in beads for persistent tracking, with markdown files as documentation.

**Workflow:**
1. Explore project context and ask clarifying questions
2. Present design in sections, validate with user
3. Create beads epic with full design in description
4. Create markdown reference file linking to epic
5. Commit both to git

**Beads Commands:**
- View design: `bd show <epic-id>`
- List designs: `bd list --type epic --label design`
- Update design: `bd update <epic-id> --description "updated content"`

**Markdown Reference:**
- Stored in `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Contains epic link and full design text
- Human-readable documentation
- Searchable and diff-friendly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matthew-reed-holden) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
