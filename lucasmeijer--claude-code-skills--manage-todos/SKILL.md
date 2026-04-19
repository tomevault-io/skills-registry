---
name: manage-todos
description: Manage development todos (todo-*.md files). Use when the user asks to create, view, update, expand, or complete a todo, or when the user mentions a file starting with "todo-". Use when this capability is needed.
metadata:
  author: lucasmeijer
---

You are managing development todos using a file-based todo system.

## Todo System Overview

Todos are markdown files with the prefix `todo-` that track work to be done. They can live anywhere in the codebase, placed near the code they relate to. Completed todos are moved to `.completedtodos/`.

## File Format

```markdown
# Human-Readable Title
parent: todo-parent-slug

## Description
What needs to be done and why. Can include natural language about:
- Ordering constraints ("profile before optimizing", "implement basic version before adding caching")
- Blockers ("waiting on API v2 migration", "blocked by dependency upgrade")
- Dependencies on other todos
- Context and rationale

## Subtasks
- [x] Completed inline subtask
- [ ] todo-child-slug
- [ ] Another inline subtask
```

Notes:
- The slug comes from the filename (e.g., `todo-fix-renderer-perf.md` has slug `todo-fix-renderer-perf`)
- No status field - if the file exists outside `.completedtodos/`, it's active
- `parent:` is optional, only used when this todo is a subtask of another
- Subtasks can be inline text or references to other todo files

## Creating a Todo

1. **Choose a slug**: Descriptive, kebab-case, starts with `todo-`
   - Good: `todo-add-user-authentication`, `todo-optimize-database-queries`, `todo-implement-caching-layer`
   - Bad: `todo-stuff`, `todo-fix-it`, `todo-things`

2. **Check for uniqueness**: Before creating, verify no existing todo has this slug
   ```bash
   find . -name "todo-*.md" | grep -i "your-slug"
   ```
   If a similar todo exists, alert the user - they may want to reuse or extend it.

3. **Choose location**: Place the todo near the code it affects
   - `src/auth/todo-add-oauth-support.md` for authentication work
   - `backend/api/todo-implement-rate-limiting.md` for API work
   - `frontend/components/todo-redesign-navbar.md` for UI work
   - Root level for cross-cutting concerns

   If unsure about location, ask the user.

4. **Create the file** with the format above

## Expanding a Todo

When asked to expand or flesh out a rough todo:
1. Read the existing todo
2. Analyze the relevant codebase to understand scope
3. Break down the description into concrete subtasks
4. Add ordering/dependency notes in natural language
5. Create child todo files for large subtasks that need their own breakdown
6. Commit the changes

## Completing a Todo

When a todo is done:
1. Move the file to `.completedtodos/`
   ```bash
   mv path/to/todo-foo.md .completedtodos/
   ```
2. Commit the move

Do NOT edit the todo file when completing - just move it.

## Listing Todos

Find all active todos:
```bash
find . -name "todo-*.md" -not -path "./.completedtodos/*"
```

Find completed todos:
```bash
ls .completedtodos/
```

## Subtask Patterns

**Inline subtasks** - for small, self-contained items:
```markdown
- [ ] Write unit tests for the new feature
- [ ] Update API documentation
- [ ] Add error handling for edge cases
```

**Todo references** - when a subtask is big enough to warrant its own file:
```markdown
- [ ] todo-implement-user-sessions
- [ ] todo-add-email-notifications
```

When to create a separate todo file:
- Subtask needs its own description paragraph
- Subtask has sub-subtasks
- Subtask might be worked on independently
- Subtask is complex enough to benefit from its own revision history

When creating a child todo:
1. Create the new todo file with `parent: todo-parent-slug`
2. Add `todo-child-slug` as a subtask line in the parent
3. Commit both changes together

## Researching a Todo

When asked to research a todo:

1. **Read the todo** to understand what it's asking for
2. **Research relevant source code** - explore the codebase to understand:
   - Current implementation
   - Related systems and dependencies
   - Patterns used elsewhere that might apply
   - Potential approaches and tradeoffs
3. **Ask clarifying questions** - use the built-in multiple choice feature (AskUserQuestion) to present the user with questions whose answers would help create a detailed plan:
   - Implementation approach preferences
   - Scope decisions (minimal vs comprehensive)
   - Performance vs simplicity tradeoffs
   - Integration points with existing code
   - Testing strategy preferences
4. **Augment the todo** with a detailed plan based on the research and user answers:
   - Add concrete subtasks
   - Note specific files/functions to modify
   - Include relevant code references
   - Document any decisions made

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucasmeijer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
