---
name: mydearmentor
description: Maintain a mentor-style StudyNotes.md during software development work. Use when the user asks to keep StudyNotes.md updated, wants step-by-step teaching notes, or when working in the mydearmentor project with ongoing development tasks. Use when this capability is needed.
metadata:
  author: qs3c
---

# MyDearMentor Study Notes

## Goal
Maintain a running StudyNotes.md as a mentor-style teaching log while doing development tasks.

## Core Rules
- Ask the user which language to use for StudyNotes.md when the skill starts, unless they already specified it.
- Use the chosen language consistently for all StudyNotes.md entries in the session.
- Translate the template section headings into the chosen language.
- Keep notes concise, practical, and focused on reasoning and decisions.
- Update StudyNotes.md throughout the work, not only at the end.
- Record every distinct CLI command used during the task; if repeated, record it only once with an explanation.

## Workflow
1. If the user did not specify a language for StudyNotes.md, ask which language to use.
2. Locate StudyNotes.md at the repository root. Create it if missing using the template below, translating headings to the chosen language.
3. Start a new session entry at the beginning of the task with date and task summary.
4. After each phase (analysis, plan, implementation, testing, review, fixes), append:
   - What was done
   - How it was done (steps)
   - Core ideas or principles used
5. Commands:
   - Record every distinct CLI command used during the task; if a command is used repeatedly, record it only once.
   - Explain what the command does and why it was used.
6. Problems and resolutions:
   - Record the problem symptoms and investigation steps.
   - Record the final fix with the exact change or command that solved it.
7. Before the final response, ensure StudyNotes.md reflects all work done.

## Template (use when creating StudyNotes.md)

# Study Notes

## Session YYYY-MM-DD
- Task:
- Context:

### Phase: Analysis
- What:
- How:
- Core ideas:

### Phase: Implementation
- What:
- How:
- Core ideas:

### Phase: Testing
- What:
- How:
- Core ideas:

### Commands
- `command`
  - Explanation:
  - Reason:

### Problems and Resolutions
- Problem:
- Investigation:
- Fix:

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qs3c) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
