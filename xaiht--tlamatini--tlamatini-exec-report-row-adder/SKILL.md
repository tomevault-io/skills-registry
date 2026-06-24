---
name: tlamatini-exec-report-row-adder
description: Add a state-changing tool to _EXEC_REPORT_TOOLS in mcp_agent.py and the matching CSS rules so its operations appear in the chat-page Exec Report. Use when this capability is needed.
metadata:
  author: XAIHT
---

# Exec Report row adder

Three-step procedure (matches `docs/claude/exec-report.md`):

1. Add an entry to `_EXEC_REPORT_TOOLS` in `Tlamatini/agent/mcp_agent.py`:
   ```python
   "${input.tool_name}": ("${input.agent_key}", "${input.agent_display}"),
   ```

2. Add CSS rules in `agent_page.css`:
   - `.exec-report-caption-${input.agent_key}` (gradient mirroring
     `.canvas-item.${input.agent_key}-agent` from agentic_control_panel.css)
   - `.exec-report-${input.agent_key} .exec-report-cmd { border-left: 3px solid <primary>; }`
   - If the caption is dark, append `.exec-report-${input.agent_key} thead th`
     to the dark-tinted override selector list.

3. Run `python Tlamatini/manage.py test agent.tests.ExecReportCaptureTests`.
   Report the pass/fail status.

Return `{ rows_added, tests_pass }`.

---
> Source: [XAIHT/Tlamatini](https://github.com/XAIHT/Tlamatini) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
