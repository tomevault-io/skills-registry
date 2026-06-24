---
name: update-documentation
description: Use when working with a guide for updating project documentation (CHANGES.md, Roadmap, etc.).
metadata:
  author: mark-friedman
---

# Update Documentation

This skill guides you through maintaining the project documentation. This is critical for tracking progress and history.

## Files to Update

### 1. `CHANGES.md`

Every time you complete a significant task (a "Walkthrough"), append an entry to `CHANGES.md`.

**Format**:
```markdown
# Walkthrough: [Title of Task] [Date]

[Brief summary of what was accomplished]

## Changes

### 1. [Category/Component]
[Description of change]
[Link to file](./relative/path/to/file)

### 2. ...

## Verification Results

### Automated Tests
[Describe test results]
```
> [!TIP]
> Copy the content from your session's `walkthrough.md` artifact if available.

### 2. `ROADMAP.md`

If the task involved R7RS compliance or roadmap items:
1.  Open `ROADMAP.md`.
2.  Mark relevant items as checked `[x]`.
3.  Add notes if necessary.

### 3. `architecture.md`

If you added, moved, or deleted files/directories:
1.  Open `architecture.md`.
2.  Update the tree structure to reflect the current state.
3.  Verify that descriptions are accurate.

### 4. `README.md`

Update if:
- New setup instructions are needed.
- Architecture overview has changed.
- New major features are available to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mark-friedman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
