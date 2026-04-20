---
name: cc-releases
description: Check Claude Code releases and updates Use when this capability is needed.
metadata:
  author: hoshinotsuyoshi
---

# Claude Code Release and Update Check

Check the latest information from sources defined in `sources.yaml` and propose things applicable to your environment.

## Configuration

- `sources.yaml` - Repositories/sources to monitor (in this skill directory)
- `sources.example.yaml` - Configuration example

## State Tracking

- `state.schema.json` - JSON Schema definition (with if/then constraints)
- `state.yaml` - Last check state (auto-created, gitignored)
- `state.example.yaml` - Example with schema reference

**Rules:**
- `last_checked` is Unix epoch seconds (UTC)
- Schema comment (`# yaml-language-server: ...`) must be preserved on updates
- Concurrent execution: last write wins (use atomic write: temp file + rename)
- Orphan keys: remove entries from `state.yaml` if source is deleted from `sources.yaml`
- Only update `last_checked` for successfully checked sources

**Recovery:**
- If `state.yaml` is corrupted, delete it and run `/cc-releases` again

## Tasks

1. Read `sources.yaml` to get source list
2. Read `state.yaml` if exists (first run: treat all as new, create `sources: {}`)
   - If parse error: warn user and offer to delete and recreate
3. Remove orphan keys from state (sources not in sources.yaml)
4. For each source:
   - If type is `releases`:
     - Run `gh release list --repo <repo> --exclude-drafts --exclude-pre-releases --limit 10`
     - If gh auth error: report and skip this source
     - Compare with `state.yaml.sources.<name>.last_version`
     - Mark releases newer than last_version as [NEW] (trust gh output order, newest first)
     - If last_version not found in list: warn and mark only latest as [NEW]
   - If type is `commits`:
     - Run `gh api repos/<repo>/commits --paginate --jq '.[].sha'` (use since param if last_checked exists)
     - If gh auth error: report and skip this source
     - If last_sha not in results: warn and mark latest 5 as [NEW]
     - Otherwise mark commits after last_sha as [NEW]
5. Pick up NEW items applicable to your environment
6. Propose specific application methods
7. Add things worth trying to CLAUDE.md "Things to Try" section
8. Ask user: "Update state.yaml to mark these as seen?"
9. If yes, update `state.yaml` (atomic write: write to temp file, then rename):
   - Preserve schema comment at top
   - Only update successfully checked sources
   - Set last_checked to current Unix epoch
   - For releases: set last_version to newest version
   - For commits: set last_sha to newest commit SHA (lowercase)

## Output Format

```
## Update Summary

### Status
- claude-code: Last check 1738540800 (3 days ago)
- awesome-claude-code: Last check 1738540800 (3 days ago)

### [claude-code] (releases)
- [NEW] v2.1.30 - Main changes
- [SEEN] v2.1.27

### [awesome-claude-code] (commits)
- [NEW] 2 commits since fe3c03d

### Errors
- (none)

## Try Today (pick one)
- What
- How

---
Update state.yaml to mark as seen? (y/n)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoshinotsuyoshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
