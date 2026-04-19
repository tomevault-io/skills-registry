---
name: inspiration-importer
description: | Use when this capability is needed.
metadata:
  author: gjkeller
---

# Inspiration Importer

Import external content into `inspiration/` for knowledge review, then produce a structured
summary so the user can decide next actions.

## Your Task

When given a source (GitHub repo, gist, URL, or file path):

1. **Import the content** — Place it under `inspiration/`
2. **Summarize the content in chat** — Include required tables
3. **Ask what to do next** — Offer clear options and wait for a choice
4. **Execute the user's chosen option**

---

## Supported Sources

- **GitHub repo**: `https://github.com/owner/repo` or `owner/repo`
- **GitHub repo path**: `https://github.com/owner/repo/tree/branch/path`
- **GitHub gist**: `https://gist.github.com/owner/hash`
- **Raw URL**: Any URL to a markdown or text file
- **Local file/folder**: Path to content already on disk

---

## Process

### Step 1: Parse the Input

For GitHub repos, extract:
- Owner
- Repo name
- Branch (default: repo default branch)
- Optional path (for partial imports)

For other sources, identify the content type and origin.

### Step 2: Prepare the Destination

Destination: `inspiration/` at the repo root.

Use a unique folder name to avoid collisions:

```
inspiration/
└── {owner}-{repo}/
    ├── _SOURCE.md      # Tracking file
    └── ...             # Imported content
```

If the destination exists:
- Ask the user whether to replace, update, or cancel.

### Step 3: Import the Content

**For GitHub repos — use git submodule:**

```bash
git submodule add https://github.com/OWNER/REPO inspiration/OWNER-REPO
```

This:
- Creates a `.gitmodules` entry (or appends to existing)
- Clones the repo at its current HEAD
- Tracks the exact commit in the parent repo

To update an existing submodule later:

```bash
cd inspiration/OWNER-REPO && git pull && cd ../..
git add inspiration/OWNER-REPO && git commit -m "chore: update OWNER-REPO submodule"
```

**For other sources — download directly:**

```bash
# Gist
curl -sL https://gist.githubusercontent.com/OWNER/HASH/raw/ > inspiration/OWNER-gist-HASH/content.md

# Raw URL
curl -sL URL > inspiration/source-name/content.md
```

### Step 4: Create Source Tracking File

Create `inspiration/{source}/_SOURCE.md`:

```markdown
# Source Information

This directory contains imported content for knowledge review.

- **Source**: SOURCE_URL
- **Type**: repo | gist | url | local
- **Branch**: BRANCH (if applicable)
- **Commit**: COMMIT_HASH (if applicable)
- **Synced at**: TIMESTAMP

## File Mapping

| Local File | Upstream Path |
|------------|---------------|
| README.md | README.md |
| (local addition) | _SOURCE.md |

## Notes

Re-run the inspiration-importer skill to update this import.
```

### Step 5: Summarize for the User

Read and summarize the import. Provide:

1. **Overview** — 2-5 bullets: purpose, structure, key entry points
2. **Skills table** — All `.cursor/skills/*/SKILL.md` entries  
   Columns: Skill, Location, Purpose
3. **Docs table** — All `docs/**/*.md` plus root `README.md` (if present)  
   Columns: Doc, Location, Purpose
4. **Other notable assets** — Scripts, templates, prompts, workflows, configs  
   Columns: Asset, Location, Purpose

Keep descriptions short and factual. Use the file description, first heading, or
intro paragraph to infer purpose. If unclear, say "Unclear from file."

### Step 6: Ask What to Do Next

Ask the user what to do with the imported knowledge and offer options:

- Import skill(s) into `.cursor/skills/`
- Add knowledge to `docs/` (summarized and reformatted to match this repo)
- Do nothing (leave the import as-is for reference)
- Delete the import
- Something else (ask)

Wait for the user's choice, then proceed.

---

## Example Usage

**Import a GitHub repo:**
```
Import https://github.com/owner/repo
```

**Import a specific path from a repo:**
```
Import https://github.com/owner/repo/tree/main/docs
```

**Import a gist:**
```
Import https://gist.github.com/owner/abc123
```

---

## Output

After importing, report:

1. **Source**: Full source URL
2. **Commit**: Hash that was synced (if applicable)
3. **Import location**: `inspiration/{source}/`
4. **Summary**: Overview + required tables
5. **Next step question**: Ask the user to choose an option

---

## Edge Cases

- **Large repos**: Import fully; summarize only key files
- **Binaries**: Keep in import but skip reading them for summary
- **Missing docs/skills**: Say "None found"
- **Existing import**: Ask before replacing or updating
- **Broken submodule** (nested `.git` but no `.gitmodules` entry): Fix with:
  ```bash
  git rm --cached inspiration/OWNER-REPO
  git submodule add https://github.com/OWNER/REPO inspiration/OWNER-REPO
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gjkeller) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
