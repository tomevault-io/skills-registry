---
name: node-dev-helper
description: Node/Express/JavaScript developer assistant – generate boilerplate, review code, and surface framework-specific issues. Trigger on "node assist", "generate controller", "review js", "help with express". Use when this capability is needed.
metadata:
  author: tal7aouy
---

# Node/Express Development Assistant

You are the orchestrator of a collection of LLM agents that help Node.js/JavaScript
engineers write applications faster and safer. The skill can generate
boilerplate (controllers, models, routes, middleware, etc.), answer
framework-specific questions, and perform lightweight reviews for
common mistakes and architectural issues.

## Invocation

Run the skill via the Claude Code CLI, the VS Code Claude extension, or
Cursor:

```
/node-dev-helper [mode] [options]
```

### Modes

- **default** (no arguments)  
  Scan all `.js` and `.ts` files in the repository (ignoring `node_modules/`,
  `vendor/`, `dist/`, `tests/`, and similar) and produce a Markdown report
  with:
  - suggestions for improvements,
  - common issues and pitfalls (callback hell, unhandled promises,
    insecure configuration), and
  - style or architectural refactorings.
- **generate <type> <name>**  
  Create boilerplate for the specified type (`controller`, `model`,
  `route`, `middleware`, `event`, `command`, etc.) using project
  context and existing code to guide naming and structure.
- **review <file ...>**  
  Analyze only the listed file(s).
- **deep**  
  Add an adversarial reasoning agent for a more thorough security
  look‑over. Slower and more costly, similar to "default" plus extra
  scrutiny.
- **help**  
  Display a short usage summary.

### Flags

- `--file-output`  
  When provided, write the analysis report or generated code to
  disk under `assets/reports/` (or other appropriate paths) instead of
  printing solely to the terminal.

## Architecture

The skill follows a lightweight orchestration pattern adapted from an
earlier template:

1. **Discover** – locate relevant source files (`*.js`, `.ts`) according to
   the selected mode.
2. **Bundle** – assemble code snippets along with reference documents
   (framework docs, coding standards, generation templates) into
   temporary bundle files under `/tmp`. Bundles are simply concatenated
   source files with `### path` headers.
3. **Spawn agents** – four parallel "vector" agents analyze the bundle
   for issues and suggestions; a dedicated "generator" agent runs in
   `generate` mode; an optional adversarial agent executes in `deep`
   mode.
4. **Merge results** – collate agent outputs, deduplicate, sort by
   confidence, and either display them or write them to disk.

Agents are standard Claude models (`sonnet` for normal work,
`opus` for adversarial reasoning). Their prompts and instructions live
in `references/agents/*.md`.

## Bundles & References

The repository ships with several reference files under
`references/` (or you can point agents at other docs if you prefer):

- coding standards and style guides for JavaScript/TypeScript
- example templates for generating routers, controllers, middleware, etc.
- miscellaneous pattern files or notes you want agents to read
- `agents/` – instruction templates for each type of agent

When the skill runs it concatenates the in-scope project files with the
chosen reference documents to create bundles. Agents read only their
bundle; they never access any other workspace files directly.

## Output

For analysis modes the output is a Markdown report containing:

- scope information (mode, files reviewed)
- reports sorted by confidence
- suggested fixes or code snippets

For `generate` mode the agent's output is either printed or written to
project files (e.g. `src/controllers/FooController.js` or
`routes/foo.js`).

> ⚠️ This assistant leverages AI. It can boost productivity and flag
> common mistakes, but it is not a substitute for human review,
> security testing, or adherence to framework documentation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tal7aouy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
