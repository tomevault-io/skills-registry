---
name: taskboard-skill
description: Manage tasks and projects using the TaskBoardAI Kanban system. Includes MCP server integration. Use when this capability is needed.
metadata:
  author: openclaw
---

<skill>
  <name>taskboard</name>
  <description>
    Manage tasks and projects using the TaskBoardAI Kanban system.
    
    CRITICAL LIFECYCLE RULES:
    1. **Create**: When a new task is identified, create it in the 'To Do' column.
    2. **Execute**: IMMEDIATELY after creating a task (or when asked to work on one), move it to 'In Progress' (Doing). Do not leave active tasks in 'To Do'.
    3. **Block**: If a task cannot be completed (e.g., missing API key, network error), move it to 'Blocked' and note the reason in the content.
    4. **Complete**: When finished, update the card content with the FINAL RESULT/SUMMARY, then move it to 'Done'.
    
    NOTE: The main body/result of the card must be stored in the 'content' field (Markdown supported), not 'description'.
  </description>
  <mcp_server>
    <command>node</command>
    <args>
      <arg>/opt/homebrew/lib/node_modules/taskboardai/server/mcp/kanbanMcpServer.js</arg>
    </args>
  </mcp_server>
</skill>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
