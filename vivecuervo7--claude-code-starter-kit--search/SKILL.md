---
name: search
description: description: Semantic code search across indexed projects using ChunkHound Use when this capability is needed.
metadata:
  author: vivecuervo7
---
---
name: search
description: Semantic code search across indexed projects using ChunkHound
argument-hint: <query>
allowed-tools: Bash, Read, AskUserQuestion
---

# Code Search

Search for "$ARGUMENTS" using ChunkHound semantic search.

## Configuration

1. Read `~/.claude/skills/chunkhound-config.json` — contains `embedding_args` (CLI flags for chunkhound) and `db_dir`.
2. Read `~/.claude/skills/projects.json` — maps project names to paths.

## Steps

1. **Read the config and registry files** listed above.

2. **Resolve the current project** from your working directory by matching against the project paths in the registry. If the cwd is inside a project path, that's the project.

3. **Check if the DB exists.** If `{db_dir}/{name}.duckdb` does not exist, index it first:

   ```bash
   chunkhound index {project_path} --db {db_dir}/{name}.duckdb {embedding_args}
   ```

4. **Run semantic search**:

   ```bash
   chunkhound search "$ARGUMENTS" --db {db_dir}/{name}.duckdb --semantic {embedding_args}
   ```

5. **If results were found**, read the relevant files to answer the user's question. Do NOT run additional grep/glob searches. Stop here.

6. **If no results were found**, use AskUserQuestion to ask:
   - Question: "No results found in {current_project}. Search across all projects?"
   - Options: The first option should be "No", followed by "All projects", then one option per OTHER registered project (only those whose DB files exist), plus the implicit "Other" option.

7. **If the user approves**, run semantic search against the selected project(s).

8. **Read the relevant files** from the results to answer the user's question.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vivecuervo7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
