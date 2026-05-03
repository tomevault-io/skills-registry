---
name: obsidian
description: Personal obsidian skill Use when this capability is needed.
metadata:
  author: madundead
---

# When to use me
When working on Personal Obsidian vault notes.

# Execution & Boundaries (CRITICAL)
- **Step-by-Step Approval**: For any operation involving multiple files, folder restructuring, or system commands, you MUST:
    1. Present a numbered list of EVERY planned action.
    2. Wait for explicit approval for the next step(s).
    3. Never perform "bonus" or "implied" tasks outside the approved scope.
- **Backups**: Before performing structural changes, remind the user to backup or offer to run the NAS backup command.
- **Read-Only First**: Always perform a read-only analysis (grep/glob) before proposing a plan.

# Behavior
- **Location**: Assume the current working directory is the Personal Vault root.
- **PARA structure**: Follow the `00_Inbox`, `05_Journal`, `10_Projects`, `20_Areas`, `30_Resources`, `40_Archives`, `80_Matic` hierarchy. Note: Daily notes are stored directly in `05_Journal/`.
- **Archive Mirroring**: Follow the mirrored PARA pattern (`40_Archives/Projects/`, `40_Archives/Areas/`, `40_Archives/Resources/`).
- **MOC rule**: If a folder contains sub-notes, ensure it has a corresponding Map of Content (MOC) note acting as its index. Update the parent MOC when adding new resources.
- **No Proactive Cleanup**: Never move, rename, or delete files unless explicitly part of an approved step.
- **NAS Backup**: Command for vault backup:
  `tar -czf ~/nas/bulk/archives/obsidian-vault-backup-$(date +%F).tar.gz -C /home/madundead/Syncthing/Obsidian/Personal`

# Standards & Organization
- **Hierarchy**: Refer to `[[STRUCTURE]]` for the PARA hierarchy and vault logic.
- **Markdown/Style**: Refer to `[[99_System/Markdown_Conventions]]` for formatting rules.
- **Tags**: Refer to `[[99_System/Tags]]` for the official tag dictionary.

# Tone and constraints
- Adopt a direct, functional, and concise tone. Strip out AI conversational filler, enthusiastic agreements, and unnecessary pleasantries.
- Before creating a new note (e.g., a Project, Area, or Topic), you MUST read the corresponding template in `99_System/Templates/` first.
- When asked to format, output valid Markdown/Obsidian markup only.
- If uncertain about a field, omit it rather than guessing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madundead) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
