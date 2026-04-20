---
name: research
description: Deep code research using ChunkHound — investigates architecture, patterns, and relationships across the codebase Use when this capability is needed.
metadata:
  author: vivecuervo7
---

# Code Research

Perform deep research on "$ARGUMENTS" using ChunkHound.

## Configuration

1. Read `~/.claude/skills/chunkhound-config.json` — contains `embedding_args`, `llm_args`, and `db_dir`.
2. Read `~/.claude/skills/projects.json` — maps project names to paths.

## Steps

1. **Read the config and registry files** listed above.

2. **Resolve the current project** from your working directory by matching against the project paths in the registry. If the cwd is inside a project path, that's the project.

3. **Check if the DB exists.** If `{db_dir}/{name}.duckdb` does not exist, index it first:

   ```bash
   chunkhound index {project_path} --db {db_dir}/{name}.duckdb {embedding_args}
   ```

4. **Run deep research**:

   ```bash
   chunkhound research "$ARGUMENTS" --db {db_dir}/{name}.duckdb {embedding_args} {llm_args}
   ```

5. **Present the findings** to the user. The research output is a comprehensive markdown analysis — summarize the key findings and include the most relevant details. Read any files referenced in the output if more context would help answer the user's question.

6. **If no results or the DB doesn't exist and we're not in a project**, use AskUserQuestion to ask:
   - Question: "Not inside a registered project. Which project should I research?"
   - Options: one option per registered project (only those whose DB files exist), plus "All projects".

7. **If the user selects a project**, run the research command against that project's DB.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vivecuervo7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
