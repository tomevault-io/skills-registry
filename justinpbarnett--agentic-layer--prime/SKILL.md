---
name: prime
description: Primes Claude with full codebase context by scanning the file tree, reading key documentation, and summarizing understanding. Use when the user says prime, learn the codebase, get familiar with the project, understand this repo, read the codebase, onboard yourself, context load, or what does this project do. Also triggers for scan the repo, explore the codebase, or orient yourself. Do NOT use for implementing features, fixing bugs, or running tests — those have dedicated workflows. Use when this capability is needed.
metadata:
  author: justinpbarnett
---

# Prime

Primes Claude with full codebase context by scanning the project file tree, reading core documentation, and producing a concise summary of understanding. This is the foundational orientation step before any development work.

## Instructions

### Step 1: Scan the File Tree

Run `git ls-files` to get a complete picture of all tracked files in the repository. This reveals the project structure, key directories, and file organization patterns.

```bash
git ls-files
```

If `git ls-files` fails (not a git repo), fall back to listing the directory tree with reasonable depth.

### Step 2: Read Core Documentation

Read the following files in order. If a file does not exist, skip it and note its absence.

1. **README.md** — Project overview, setup instructions, high-level architecture
2. **adws/README.md** — ADW layer documentation, module responsibilities, workflow patterns
3. **.claude/commands/conditional_docs.md** — Guide for determining which additional documentation to read based on upcoming tasks. If conditions are configured, follow them and read any referenced documentation files.

### Step 3: Synthesize and Summarize

After reading all files, produce a concise summary that covers:

- **What the project is** — Core purpose and domain
- **How it's structured** — Key directories and their roles
- **How to run it** — Available commands, scripts, entry points
- **Key patterns** — Conventions, naming schemes, architectural decisions
- **Technology stack** — Languages, frameworks, tools, dependencies
- **What to watch out for** — Gotchas, safety constraints, important conventions

## Validation

Before producing the summary, verify:
- You successfully scanned the file tree (got a list of tracked files)
- You read at least one documentation file successfully
- You checked conditional_docs.md for any task-specific documentation requirements
- Your summary accurately reflects what you read (no hallucinated details)

## Examples

### Example 1: Standard Priming
**User says:** "Prime yourself on this codebase"
**Actions:**
1. Run `git ls-files` to scan the project
2. Read README.md, adws/README.md, and conditional_docs.md
3. Summarize the project's purpose, structure, commands, and conventions
**Result:** A concise, accurate summary of the codebase that enables informed development work

### Example 2: New Project Orientation
**User says:** "Get familiar with this repo before we start working"
**Actions:**
1. Run `git ls-files` to discover the project layout
2. Read all available documentation files
3. Check conditional_docs.md for any additional reading
4. Produce a structured summary covering architecture, commands, and conventions
**Result:** Comprehensive project orientation ready for follow-up tasks

### Example 3: Context Refresh
**User says:** "Re-read the codebase, I've made changes"
**Actions:**
1. Re-scan `git ls-files` for any new or removed files
2. Re-read documentation files for updated content
3. Produce an updated summary noting any changes from prior understanding
**Result:** Refreshed context reflecting the current state of the codebase

## Troubleshooting

### Error: git ls-files fails
**Cause:** The working directory is not a git repository
**Solution:** Fall back to `find . -type f` or `ls -R` with reasonable depth limits. Note in the summary that this is not a git-tracked project.

### Error: README.md not found
**Cause:** The project may not have a root-level README, or documentation may live elsewhere
**Solution:** Skip the missing file and note its absence. Look for alternative documentation (e.g., docs/, wiki/, CONTRIBUTING.md, or package.json description fields).

### Error: Conditional docs references missing files
**Cause:** The conditional_docs.md may reference files that have been moved or deleted
**Solution:** Note the missing references in the summary so the user is aware. Continue with available documentation.

## Performance Notes
- Take your time to read thoroughly — understanding the codebase correctly is critical
- Quality of the summary is more important than speed
- Do not skip the conditional_docs.md step, even if no conditions are configured yet

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinpbarnett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
