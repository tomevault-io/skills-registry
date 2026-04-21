---
name: reflect
description: Structured reflection after complex work — extract insights, patterns, and concrete next actions from a session. Use when "reflect on this", "what did we learn", "session recap". Use when this capability is needed.
metadata:
  author: lucadellanna
---

Review a work session to extract what worked, what did not, and what to do next. Present findings, then implement approved actions.

Output format is defined in `template.md` (same folder). Follow it for presentation; modify procedure here only.

**Structured questions**: When asking the user to choose between options (scope clarification, action selection), prefer the `AskUserQuestion` tool if available. It surfaces choices as selectable options in the UI. Fall back to inline text when the tool is unavailable.

---

## Phase 1 — Analyze

### 1. Identify scope

Determine what to reflect on.

- If the user names a specific task or session, use that.
- If the conversation contains substantial work (multi-step task, debugging, design discussion), reflect on that.
- If the conversation is short or routine, ask whether to proceed (options: "Proceed anyway" / "Skip"). Include context: "This looks like a routine task — reflection is most useful after complex work."
- When unclear, summarize what you see and ask which scope to use (options: the inferred scope / "Something else").

### 2. Reconstruct the timeline

Walk through what happened, briefly:

- What was the goal?
- What steps were taken?
- Where were the decision points — moments where a different choice would have led somewhere else?
- What changed direction midway (pivots, discoveries, failures)?

Keep this short. It is context for the insights, not the deliverable.

### 3. Extract insights

For each category in the **Insight categories** section of `template.md`, be specific — reference actual files, functions, error messages, or decisions from the session. Skip any category that produced nothing non-obvious.

### 4. Propose actions

For each insight with a concrete next step, propose an action using the **Action types** from `template.md`. Only propose actions where the benefit is clear. Not every insight needs a follow-up.

---

## Phase 2 — Act

### 5. Present for review

Show the full reflection: timeline summary, insights, and proposed actions. Number each action.

Ask which actions to take. Use `AskUserQuestion` if available, with options like "All", "None", and individual action numbers. Otherwise accept natural language: "do all of them", "skip 3", "tell me more about 2", "none for now."

If the user selects none, end here. The reflection itself has value.

If the user wants to save the reflection, write it using the format in `template.md` § **Saved reflection format**. Default location: working directory, named `reflection-YYYY-MM-DD.md` (append `-N` if the date already exists). If a `.context/` directory exists, use `.context/reflections/` instead.

### 6. Implement selected actions

For each approved action, preview the change and confirm before applying.

- **Add to CLAUDE.md**: Show the exact addition and where it goes. Confirm, then apply.
- **Update a skill**: Describe the change, preview it, confirm, then apply. Back up modified files first into `{skill_folder}/.backups/{ISO-8601-timestamp}/` (replace `:` with `.` in timestamps). Run `/skills-review` on the result.
- **Create a new skill**: Invoke `/create-skill` with the intent. It handles structure, registration, and review.
- **Add to framework**: Show the proposed addition and target file. Confirm, then apply. Only relevant when working inside a skills framework repo.
- **Persist to memory**: In Claude Code, write to the auto-memory directory (`~/.claude/projects/.../memory/`). In environments without persistent memory, add to the project's CLAUDE.md instead. State where the note was saved.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucadellanna) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
