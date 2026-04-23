---
name: writing-instructions
description: > Use when this capability is needed.
metadata:
  author: j03fr0st
---

# Writing Instruction Files

Create `.instructions.md` files in `.github/instructions/` that automatically
apply guidelines when matching files are edited. Instruction files let teams
enforce conventions consistently without relying on memory or code review alone.

## File Types

| Type | Location | Scope |
|------|----------|-------|
| `copilot-instructions.md` | `.github/` root | All chat requests — use for project-wide standards |
| `*.instructions.md` | `.github/instructions/` | Files matching `applyTo` glob — use for language or domain-specific rules |
| `AGENTS.md` | Workspace root | Multi-agent workflows |

## File Format

Every instruction file needs YAML frontmatter followed by Markdown content:

```markdown
---
applyTo: "**/*.ts"
description: TypeScript coding standards
name: TypeScript Guidelines
---

# TypeScript Guidelines

- Use `interface` over `type` for object shapes — interfaces produce clearer
  error messages and support declaration merging.
- Prefer `unknown` over `any` — this forces explicit type narrowing and catches
  bugs at compile time.
- Export types from the same file as their implementation — keeps imports simple
  and avoids circular dependencies.
```

## Frontmatter Fields

| Field | Purpose |
|-------|---------|
| `applyTo` | Glob pattern that determines when this file is automatically included. Omit for manual-only inclusion. |
| `description` | Short description shown in the Copilot UI to help users understand what this file covers. |
| `name` | Display name in the UI (defaults to the filename if omitted). |

## Common Glob Patterns

| Pattern | Matches |
|---------|---------|
| `**` | All files |
| `**/*.ts` | All TypeScript files |
| `**/*.component.ts` | Angular components |
| `src/**` | All files in src/ |
| `**/*.{js,ts}` | JS and TS files |
| `**/tests/**` | All test files |
| `**/*.{css,scss}` | Stylesheets |

## Body Content

- Write in natural language using Markdown.
- Keep instructions short, concise, and actionable — Copilot processes these on
  every matching file edit, so brevity reduces noise.
- Use bullet points over paragraphs — they are easier to scan and less likely to
  contain contradictions.
- Reference files with relative links: `[config](../path/to/file.json)` — this
  gives Copilot direct access to referenced content.
- Reference tools inline: `#tool:<tool-name>`.

## Rules

- **One file per topic or language** — splitting by concern makes each file
  easier to maintain and prevents unrelated rules from coupling together.
- **Use specific glob patterns** — overly broad `applyTo` (like `**`) applies
  your rules to every file, which adds noise and can cause conflicting guidance
  across instruction files.
- **Do not duplicate instructions across files** — duplication leads to drift
  when one copy is updated but not the other. Reference shared rules from a
  single source instead.
- **Do not include secrets or credentials** — instruction files are version
  controlled and visible to all collaborators.
- **Store project-specific instructions in the workspace** — this ensures they
  travel with the repo and stay version controlled.
- **Reference other instruction files in prompts** — if a prompt needs the same
  guidelines, link to the instruction file rather than copying its content.

## Conflict Resolution and Precedence

When multiple instruction files match the same file, Copilot merges them. Handle
conflicts by:

1. **More specific globs win** — `**/*.component.ts` is more targeted than
   `**/*.ts`. Put specific overrides in the narrower file.
2. **Do not contradict across files** — if two files disagree, Copilot has no
   guaranteed precedence. Resolve conflicts by consolidating the rules into one
   file or splitting the globs so they do not overlap.
3. **Project-level (`copilot-instructions.md`) sets defaults** — use it for
   universal standards. Use scoped instruction files for language or
   domain-specific overrides.

## Complete Example

A real-world instruction file for a React project:

```markdown
---
applyTo: "**/*.tsx"
description: React component conventions for the design system
name: React Components
---

# React Component Standards

## Structure
- One component per file. Name the file to match the default export.
- Place components in `src/components/<ComponentName>/` with an `index.ts`
  barrel export.

## Props
- Define props as an interface named `<ComponentName>Props`.
- Destructure props in the function signature, not in the body.
- Provide JSDoc comments for non-obvious props.

## Styling
- Use CSS Modules (`*.module.css`). Do not use inline styles except for
  truly dynamic values (e.g., calculated positions).
- Follow the design token system in [tokens](../src/styles/tokens.ts).

## Testing
- Co-locate test files as `<ComponentName>.test.tsx`.
- Test user-visible behavior, not implementation details.
```

## Related Skills

See the **writing-prompts** skill for creating reusable `/` commands that can
reference instruction files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/j03fr0st) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
