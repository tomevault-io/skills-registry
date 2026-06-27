---
name: rb-brainstorm
description: >- Use when this capability is needed.
metadata:
  author: kylesnowschwartz
---

# Board Brainstorming

Explore a fuzzy idea and produce a design doc that feeds into `/rb-planning`.

## Phase 1: Context Gathering

Before asking questions, build context silently:

1. Read board state: `bl ready` and `bl list --status backlog` (never full `bl list` which dumps the large done column). Note existing cards to avoid duplicating work.
2. Read CLAUDE.md for project architecture and conventions.
3. Scan `.agent-history/` directory listing for prior designs and investigations.
4. If `$ARGUMENTS` is empty, use `AskUserQuestion` to ask: "What are you thinking about building or solving?"

## Phase 2: Structured Q&A

Ask questions one at a time using `AskUserQuestion`. Multiple choice preferred over open-ended when possible. Cover these areas, adapting to what's relevant:

1. **Goal** (always ask): "What's the goal? (1 sentence describing the outcome, not the mechanism)"
   - Examples: "Make webhook ingestion reliable", "Add card tagging to the TUI"
2. **Non-goals** (always ask): "What's explicitly out of scope?"
   - Examples: "Not changing the database schema", "Not building a web UI"
3. **Constraints** (ask if applicable): "Any constraints? (Performance budgets, compatibility requirements, forbidden approaches)"
4. **Key uncertainty** (ask if applicable): "What's the biggest unknown or risk?"
5. **Prior art** (ask if applicable): "Are there existing patterns in the codebase or reference projects to follow?"

Stop when enough context exists to differentiate approaches (typically 3-5 questions). Skip questions the user already answered in `$ARGUMENTS`.

## Phase 3: Approach Proposal

Propose 2-3 approaches:
- Lead with the recommended approach and explain why
- Keep each approach to 3-5 sentences
- Include concrete tradeoffs (not vague "more flexible" / "simpler")
- Ask user to select via `AskUserQuestion` with lettered options (a/b/c)

## Phase 4: Design Doc Review

Before saving, present the draft design doc to the user section by section:
- Show each section (Problem, Non-Goals, Approach, Architecture, Decisions, Open Questions)
- Ask after each: "Does this capture it correctly?"
- Revise based on feedback before moving on

This review loop catches misunderstandings before they propagate to planning and execution.

## Phase 5: Design Doc Output

1. Run `mkdir -p .agent-history/` (never assume it exists)
2. Write the design doc to `.agent-history/YYYY-MM-DD-<topic>-design.md`
3. Use the template from `references/design-doc-template.md`
4. Present the doc path to the user

## Phase 6: Handoff

Ask: "Design doc saved to `<path>`. Want to create board cards now with `/rb-planning`, or stop here to review first?"

If the user wants to continue, invoke the Skill tool: `skill: "rb-planning", args: "<design-doc-path>"`

Stopping is a valid end state. The design doc sits in `.agent-history/` for future reference.

## Principles

- **Board-aware**: Check existing cards to avoid duplicating work already on the board.
- **One question at a time**: Never ask multiple questions in a single message.
- **Multiple choice preferred**: Easier to answer than open-ended when options are knowable.
- **The skill is not a mode**: It's a one-shot interactive workflow. No hooks, no flag files.

---
> Source: [kylesnowschwartz/ralph-ban](https://github.com/kylesnowschwartz/ralph-ban) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
