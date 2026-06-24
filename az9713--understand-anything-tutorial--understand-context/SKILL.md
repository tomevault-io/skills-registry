---
name: understand-context
description: Generate an architecture summary from the knowledge graph and inject it into CLAUDE.md, AGENTS.md, or .github/copilot-instructions.md so every agent session starts with codebase context Use when this capability is needed.
metadata:
  author: az9713
---

# /understand-context

Reads `.understand-anything/knowledge-graph.json` (and `domain-graph.json` if present) and writes a structured architecture summary into the platform-appropriate context file. Run once after `/understand` — every subsequent agent session will have ambient architectural context without needing to query the graph.

## Instructions

1. **Check the graph exists.**
   ```bash
   [ -f .understand-anything/knowledge-graph.json ] || { echo "No knowledge graph found. Run /understand first."; exit 1; }
   ```

2. **Read the graph data.** Use grep/jq to extract:
   - `project` section: name, description, languages, frameworks, analyzedAt, gitCommitHash (first 8 chars)
   - `layers` array: id, name, description, nodeIds length per layer
   - `tour` array: order, title, first sentence of description
   - File nodes with `"complexity": "complex"` — complexity hotspots
   - File nodes tagged `"entry-point"` — key entry points

3. **Detect the target context file** (check in this order):
   - `[ -f CLAUDE.md ]` → target is `CLAUDE.md`
   - `[ -f AGENTS.md ]` → target is `AGENTS.md`
   - `[ -f .github/copilot-instructions.md ]` → target is `.github/copilot-instructions.md`
   - None exist → target is `AGENTS.md` (create it)

4. **Check for existing markers** in the target file:
   - If `<!-- understand-anything:start -->` and `<!-- understand-anything:end -->` are both present: replace only the content between them (inclusive of the marker lines)
   - If absent: append the full block to the end of the file

5. **Write the summary block** with this exact format:

   ```
   <!-- understand-anything:start -->
   ## Codebase Architecture (understand-anything)

   **Project:** {name} — {description}
   **Languages:** {languages joined by ", "}   **Frameworks:** {frameworks joined by ", " or "none detected"}
   **Last analyzed:** {analyzedAt formatted as YYYY-MM-DD} (commit {first 8 chars of gitCommitHash})
   **Graph:** `.understand-anything/knowledge-graph.json`

   ### Architectural layers
   | Layer | Files | Purpose |
   |---|---|---|
   {one row per layer: | {layer.name} | {nodeIds.length} | {layer.description} |}

   ### Key entry points
   {For each file node tagged "entry-point": - `{filePath}` — {summary}}
   {If no entry-point tagged nodes: list the first 3 file nodes from the first tour step}

   ### Complexity hotspots (approach with care)
   {For each file node with complexity "complex": - `{filePath}` — {summary}}
   {If no complex nodes: write "No files currently marked complex."}

   ### Learning path ({tour.length} steps)
   {For each tour step: {order}. **{title}** — {first sentence of description}}
   <!-- understand-anything:end -->
   ```

6. **If `domain-graph.json` exists**, insert a "### Business Domains" section before the closing marker:
   ```
   ### Business Domains
   {For each node with type "domain": - **{name}**: {summary}}
   ```

7. **Report to the user:**
   ```
   ✓ Architecture summary written to {target file}
     Layers: {N}  Entry points: {N}  Hotspots: {N}  Tour steps: {N}

   Every agent session in this project will now start with codebase context.
   Re-run /understand-context after /understand to keep it fresh.
   ```

---
> Source: [az9713/understand-anything-tutorial](https://github.com/az9713/understand-anything-tutorial) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
