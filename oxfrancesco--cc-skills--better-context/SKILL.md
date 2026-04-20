---
name: better-context
description: Set up btca (Better Context) in a project by scanning dependencies, proposing resources, creating btca.config.jsonc, updating AGENTS.md, and explaining usage modes. Use when asked to configure btca/better-context for a repo. Use when this capability is needed.
metadata:
  author: oxfrancesco
---

# Better Context (btca) Setup

Set up btca for a project by scanning dependencies, proposing resources, and creating btca.config.jsonc with the approved list. Follow the confirmation steps exactly and update AGENTS.md based on the chosen usage mode.

## Step 1: Scan dependencies

- Inspect the project root `package.json` (and lockfiles if needed) to identify frameworks, libraries, and tools worth adding as btca resources.
- Look for frameworks, backends, styling, tooling, and major libraries.

## Step 2: Present the full resource list

- Show the complete list at once.
- Ask the user to confirm additions/removals.
- Use camelCase resource names (no hyphens).
- Wait for confirmation before preparing the config.

## Step 3: Prepare the config (do not write yet)

- Draft `btca.config.jsonc` with `$schema` and all approved resources.
- Use sensible defaults for URL, branch, and searchPath/searchPaths when known.
- Show the full config to the user and ask for approval before writing.

## Step 4: Create files after approval

- Create `btca.config.jsonc` in the project root.
- Add `.btca` to `.gitignore` (create the file if it does not exist).

## Step 5: Ask for usage mode

Ask which mode the agent should follow:

- eager: use btca automatically when needed
- ask: ask the user before using btca
- lazy: only use btca when explicitly requested

Wait for the response before editing AGENTS.md.

## Step 6: Update AGENTS.md

- If `AGENTS.md` exists, update or add a `## btca` section without duplication.
- If it does not exist, create it with the btca section.
- Use the template that matches the chosen mode.
- Include the approved resource list and usage examples.

## Step 7: Provide a summary

Include:

1. Configured resources (name, type, URL/path)
2. Config location (absolute path)
3. AGENTS.md status and chosen mode
4. Example btca commands for this project
5. Next steps:
   - Resources clone to `~/.local/share/btca/resources/` on first use
   - Use `btca clear` to remove cached repos
   - If using Cursor: `mkdir -p .cursor/rules && curl -fsSL "https://btca.dev/rule" -o .cursor/rules/better_context.mdc && echo "Rule file created."`

## Behavior rules

- Work in batches, not one-by-one.
- Only two confirmations: the resource list, then the prepared config.
- Do not write files before explicit approval.
- Do not add unapproved resources.
- Ask if any required detail (URL, branch, search path) is missing or ambiguous.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxfrancesco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
