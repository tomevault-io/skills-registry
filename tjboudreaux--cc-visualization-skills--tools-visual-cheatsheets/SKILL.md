---
name: tools-visual-cheatsheets
description: Build portable ASCII command panels summarizing CLI workflows (e.g., GitHub CLI, deployment scripts) for quick reference. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# CLI Cheat Sheet ASCII Panels

## Intent
- Provide glanceable command guides embedded in skills, PRs, or READMEs.
- Keep macOS/Linux and Windows variants side by side for parity.

## Inputs
1. Task categories (setup, sync, review, deploy).
2. Command variants (bash/zsh vs PowerShell/CMD).
3. Notes on flags, prerequisites, environment vars.

## Workflow
1. **Select layout**
   - Use table-like boxes:
     ```
     +------------------------+
     | Action | macOS | Win  |
     +------------------------+
     ```
2. **Populate commands**
   - Keep lines ≤ 70 chars; escape PowerShell variables properly.
   - Example row:
     ```
     | Clone | gh repo clone org/repo ~/code | gh repo clone org/repo $env:USERPROFILE\code |
     ```
3. **Highlight critical flags**
   - Use `*` or uppercase label; footnotes for risky commands.
4. **Add legend + context**
   - Explain placeholders (`<branch>`), environment setups.
5. **Reuse**
   - Store panels in `.factory/cheatsheets/<topic>.txt` and link from relevant skills.

## Verification
- Panels align properly in fixed-width fonts (test in GH preview).
- Include both macOS/Linux and Windows commands where relevant.
- Commands tested recently; update date noted in panel footer.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
