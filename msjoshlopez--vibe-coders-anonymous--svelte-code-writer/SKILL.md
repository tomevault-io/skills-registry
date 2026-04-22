---
name: svelte-code-writer
description: Use Svelte MCP tools for documentation lookup and code validation. MUST be used whenever creating or editing any Svelte component (.svelte) or Svelte module (.svelte.ts/.svelte.js). Use when this capability is needed.
metadata:
  author: msjoshlopez
---

# Svelte Code Writer

This skill provides access to official Svelte tooling via MCP (Model Context Protocol). Use these tools to ensure code correctness and access up-to-date documentation.

## Available MCP Tools

You have direct access to these Svelte MCP tools. Call them as tools, NOT as bash commands.

### 1. List Documentation Sections

**Tool:** `mcp__svelte__list-sections`

Lists all available Svelte 5 and SvelteKit documentation sections. Each section includes use cases to help identify relevant docs.

**When to use:** Before looking up documentation, to find the right section names.

---

### 2. Get Documentation

**Tool:** `mcp__svelte__get-documentation`

**Parameters:**

- `section` (required): Section name(s) to retrieve. Can be a single string or array of strings.

**Examples:**

```
section: "$state"
section: ["$state", "$derived", "$effect"]
section: "routing"
section: ["form-actions", "load"]
```

**When to use:**

- Unsure about Svelte 5 syntax
- Need to verify runes usage
- Looking up SvelteKit patterns (routing, load functions, etc.)
- Checking transition/animation APIs

---

### 3. Svelte Autofixer

**Tool:** `mcp__svelte__svelte-autofixer`

Analyzes Svelte code and returns suggestions to fix issues.

**Parameters:**

- `code` (required): The Svelte component source code
- `desired_svelte_version` (required): Use `5` for Svelte 5 projects
- `filename` (optional): Component filename (e.g., "Button.svelte")
- `async` (optional): Set `true` if using async Svelte mode

**When to use:**

- After writing any Svelte component
- Before committing changes
- When debugging reactivity issues
- To catch Svelte 4 syntax in Svelte 5 projects

---

### 4. Playground Link Generator

**Tool:** `mcp__svelte__playground-link`

Generates a Svelte REPL playground link for code sharing.

**Parameters:**

- `name` (required): Name for the playground
- `tailwind` (required): Set `true` if code uses Tailwind CSS
- `files` (required): Object mapping filenames to content

**Example:**

```json
{
	"name": "Counter Example",
	"tailwind": true,
	"files": {
		"App.svelte": "<script>let count = $state(0);</script>\n<button onclick={() => count++}>{count}</button>"
	}
}
```

**When to use:**

- User asks to share or preview code
- Creating examples for documentation
- Debugging with minimal reproduction

---

## Recommended Workflow

### When Creating a New Component

1. **Check docs if needed:** Use `list-sections` then `get-documentation` for relevant topics
2. **Write the component** using Svelte 5 patterns
3. **Validate:** Run `svelte-autofixer` on the completed code
4. **Fix any issues** the autofixer identifies
5. **Re-validate** until no issues remain

### When Editing an Existing Component

1. **Read the file** to understand current implementation
2. **Make changes** using Svelte 5 syntax
3. **Validate:** Run `svelte-autofixer` on the updated code
4. **Fix issues** if any are found

### When Debugging

1. Run `svelte-autofixer` on the problematic component
2. Check relevant docs with `get-documentation`
3. Apply fixes based on suggestions

---

## Common Documentation Lookups

| Task             | Sections to Fetch                  |
| ---------------- | ---------------------------------- |
| State management | `$state`, `$derived`, `$effect`    |
| Component props  | `$props`, `$bindable`              |
| Routing          | `routing`, `load`                  |
| Forms            | `form-actions`, `$app/forms`       |
| Transitions      | `transition:`, `svelte/transition` |
| Lifecycle        | `$effect`, `lifecycle-hooks`       |
| SSR/SSG          | `page-options`, `adapter-static`   |
| Navigation state | `$app/state`, `$app/navigation`    |
| Advanced state   | `svelte/reactivity`, `$state`      |

> **Tip:** Use `mcp__svelte__list-sections` first to see all available sections with their use cases, then fetch the relevant ones.

---

## Important Notes

1. **Always use Svelte 5 syntax** - This project uses runes (`$state`, `$props`, etc.)
2. **Always validate before finalizing** - Run autofixer on every component
3. **These are MCP tools, not CLI commands** - Call them directly as tools
4. **Check package.json for version** - Read it to confirm Svelte version if uncertain
5. **Autofixer returns suggestions** - Review and apply fixes manually, then re-run until clean

## Mandatory Validation Workflow

**BEFORE marking any Svelte task complete:**

1. Run `mcp__svelte__svelte-autofixer` with the full component code
2. Review suggestions returned
3. Fix any issues identified
4. Re-run autofixer until no issues remain
5. Only THEN respond to the user

This is NOT optional. Skipping validation leads to broken code that frustrates users.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/msjoshlopez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
