---
name: incremental-writes
description: > Use when this capability is needed.
metadata:
  author: agentcto
---

# Incremental Writes

Always decompose file work into multiple focused tool calls rather than one
large write. Keep each call to a single logical change.

## When to Apply

Apply these rules on **every** task that involves writing or editing files — not only when a file is already known to be large:

- **Any edit to an existing file** → use `StrReplace`; only fall back to `Write` when the entire file must be replaced and no targeted edit is possible
- **Creating any new file** → estimate scope first; if likely >150 lines, use skeleton-first
- **Refactoring or rewriting a section** → target the minimal region, apply incrementally

When in doubt about whether a new file will be large, default to skeleton-first.

## Rules

- **Prefer `StrReplace` over `Write` for existing files.** Only use `Write` on
  an existing file when the entire file must be replaced and no targeted edit
  is possible (e.g. `StrReplace` cannot find a unique match).
- **Never write more than ~150 lines in a single `Write` or `StrReplace` call.**
  If the content is longer, break it into sections and apply them sequentially.
- **Read before editing.** Always read the target file (or the relevant section)
  before making any edit to an existing file.
- **One logical change per call.** Each `StrReplace` should represent one
  coherent change: a function, a config block, an import group, etc.

## Strategy: Editing Existing Files

1. Read the file (or the relevant range) first.
2. Identify the minimal region that needs to change.
3. Use `StrReplace` with a `old_string` that uniquely identifies that region.
4. Apply one change per call. Chain calls for multiple independent changes.

Never reconstruct the entire file from scratch just to change a few lines.

**Example decomposition for a 3-function edit:**

```
Call 1: StrReplace — update function A
Call 2: StrReplace — update function B
Call 3: StrReplace — add import at top
```

## Strategy: Creating New Large Files

Use the **skeleton-first** pattern:

1. **Write the skeleton** — create the file with top-level structure only:
   stubs, empty functions, section headers, placeholder comments.
   Keep this under ~80 lines.
2. **Fill each section** — use `StrReplace` to replace each placeholder with
   its full implementation, one section at a time.
3. **Verify** — read the completed file and fix any issues with targeted
   `StrReplace` calls.

```
Call 1: Write        — skeleton (imports, class shell, empty methods)
Call 2: StrReplace   — implement method A
Call 3: StrReplace   — implement method B
Call 4: StrReplace   — implement method C
Call 5: StrReplace   — fill in config block
```

## Decomposition Patterns

Choose a split boundary that matches the file's natural structure:

| File type | Split by |
|-----------|----------|
| Class / module | One method or property group per call |
| Config file | One top-level key block per call |
| React component | Component shell → JSX → hooks → helpers |
| Test file | One `describe` block per call |
| Long script | Imports → constants → functions → main block |
| Markdown / docs | One major section (`##` heading) per call |

When sections have dependencies (e.g., a helper used by two methods), write
the dependency first.

## Examples

### Example 1: Editing an existing file

User says: "Add input validation to the `createUser` function and update
its error messages."

Actions:
1. Read the file to locate `createUser` and its current error strings.
2. `StrReplace` — replace the function body with the validated version.
3. `StrReplace` — update error message constants if they live elsewhere.

Result: Two focused edits; the rest of the file is untouched.

---

### Example 2: Creating a new 300-line service module

User says: "Create a `PaymentService` class with methods for charging,
refunding, and webhooks."

Actions:
1. `Write` — skeleton with class declaration, constructor stub, and three
   empty method stubs (~40 lines).
2. `StrReplace` — implement `charge()` method (~60 lines).
3. `StrReplace` — implement `refund()` method (~50 lines).
4. `StrReplace` — implement `handleWebhook()` method (~70 lines).
5. `StrReplace` — add private helper methods and final imports (~40 lines).

Result: File is built in five coherent steps; each step is reviewable and
recoverable if something goes wrong.

---

### Example 3: Rewriting a large section inside an existing file

User says: "Rewrite the `parseConfig` function — it needs to handle nested
keys now."

Actions:
1. Read the file to capture the full current `parseConfig` function body.
2. `StrReplace` — replace the entire function with the new implementation.
   If the new implementation is > 150 lines, split it: replace with a
   shorter version first, then `StrReplace` the placeholder sections.

Result: Only `parseConfig` changes; nothing else in the file is at risk.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentcto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
