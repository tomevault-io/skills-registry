---
name: agent-log
description: Log session activity to the vault's AGENT_ACTIVITY.md ledger. Use at the end of every session to ensure cross-agent context and updates are tracked across all AI agents (Gemini, Pi, Claude, etc.). Use when this capability is needed.
metadata:
  author: namanur
---

# Agent Log Skill

This skill automates the process of appending session summaries to the `00_daily_logs/AGENT_ACTIVITY.md` ledger.

## Workflow

1. **Identify Changes:** Scan the current session for all file creations, modifications, and significant decisions.
2. **Format Entry:** Use the following template, replacing the placeholders:
   ```markdown
   ---
   ## [Gemini CLI] - {{date}} {{time}}
   **Session Goal:** {{goal}}
   **Changes Made:**
   {{changes}}
   **Next Steps for next agent:** {{next_steps}}
   ```
3. **Write to Ledger:** Append this entry to the top of `00_daily_logs/AGENT_ACTIVITY.md`, immediately below the header lines.
4. **Update Daily Log:** Add a reference in `00_daily_logs/{{date}}.md` mentioning that the agent activity log has been updated.

## Guidelines
- Keep change descriptions concise.
- Use absolute or vault-relative paths for files.
- Be explicit about "Next Steps" so the next agent (even if it's not Gemini) knows exactly where to resume.

---
> Source: [namanur/Notes](https://github.com/namanur/Notes) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
