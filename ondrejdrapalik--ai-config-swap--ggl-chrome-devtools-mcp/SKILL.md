---
name: ggl-chrome-devtools-mcp
description: This skill provides workflows for Chrome browser automation, debugging, and performance analysis using ggl-chrome-devtools-mcp. Use when inspecting pages, debugging console errors, recording performance traces, or automating browser interactions. Use when this capability is needed.
metadata:
  author: ondrejdrapalik
---

<essential_principles>

<tool_prefix>
All tools are prefixed with `mcp__chrome-devtools__`. Always use the full prefix.
</tool_prefix>

<debug_profile>
**Dedicated debug profile location:**
```
~/GitHub/chrome-debug-profile
```

This profile is separate from the user's main Chrome profile. It persists across sessions but is isolated for debugging.
Located outside `.claude/` to avoid Claude Code crashes from Chrome's socket files.
</debug_profile>

<connection_flow>
**BEFORE attempting any MCP tool, check if Chrome debug instance is running:**

```bash
curl -s http://127.0.0.1:9222/json/version
```

**Response interpretation:**

| Response | Meaning | Action |
|----------|---------|--------|
| JSON with `"Browser": "Chrome/..."` | Chrome ready | Proceed with MCP tools |
| No response / connection refused | Port free | Start Chrome (see below) |
| `404 Not Found` | **PORT CONFLICT** - another process using 9222 | Follow port conflict recovery |
| Timeout | Something blocking | Follow port conflict recovery |

**If port is free, start Chrome:**
```bash
nohup /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --remote-debugging-port=9222 \
  --user-data-dir="$HOME/GitHub/chrome-debug-profile" \
  --restore-last-session \
  --no-first-run \
  --disable-background-networking \
  --disable-component-update \
  >/dev/null 2>&1 &
```

Wait 3 seconds after starting, then proceed with MCP tools.

**Do NOT:**
- Kill the user's main Chrome browser (without debug profile)
- Use `/tmp/` for the profile (not persistent)
- Skip the pre-connection check and let MCP tools fail first
</connection_flow>

<port_conflict_recovery>
**When port 9222 returns "404 Not Found" or non-Chrome response:**

This indicates Node.js processes, stale Chrome instances, or other services occupying the port.

**Step 1: Identify what's using the port**
```bash
lsof -i :9222 2>/dev/null | head -10
```

**Step 2: Interpret results and act**

| Process | Safe to kill? | Action |
|---------|---------------|--------|
| `node` | YES | `kill -9 <PID>` - likely stale MCP server |
| `Google Chrome` with debug profile path | YES | This is our debug Chrome, restart it |
| `Google Chrome` without debug profile | NO | User's main browser - use different port |

**Step 3: Kill conflicting processes (node or debug Chrome only)**
```bash
# Kill specific PIDs identified in step 1
kill -9 <PID1> <PID2> 2>/dev/null; sleep 1
```

**Step 4: Verify port is free**
```bash
curl -s http://127.0.0.1:9222/json/version
# Should return "connection refused" or empty
```

**Step 5: Start Chrome fresh**
```bash
nohup /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --remote-debugging-port=9222 \
  --user-data-dir="$HOME/GitHub/chrome-debug-profile" \
  --restore-last-session \
  --no-first-run \
  --disable-background-networking \
  --disable-component-update \
  >/dev/null 2>&1 &
sleep 3
```

**Step 6: Verify Chrome is responding correctly**
```bash
curl -s http://127.0.0.1:9222/json/version | head -1
# Must return JSON with "Browser": "Chrome/..."
```
</port_conflict_recovery>

<critical_rules>
**ALWAYS open a new tab first:**
```
1. new_page → creates fresh tab (NEVER navigate in user's current tab)
2. navigate_page → load URL in the new tab
3. Execute task
```
</critical_rules>

<prohibitions>
- **Never use Playwright-based MCP tools** (`mcp__plugin_compound-engineering_pw__*`) - this skill uses `mcp__chrome-devtools__*` exclusively
- Never kill the user's main Chrome browser (only kill debug profile Chrome or node processes on port 9222)
- Never navigate in existing tabs (always `new_page` first)
- Never use `take_screenshot` as primary inspection (use `take_snapshot` instead)
- Never create the debug profile inside `.claude/` - always use `~/GitHub/chrome-debug-profile`
</prohibitions>

<inspection_priority>
| Method | Use Case | Reliability |
|--------|----------|-------------|
| `take_snapshot` | Page structure, element UIDs for interaction | High |
| `evaluate_script` | Extract text, query DOM, check visibility | High |
| `take_screenshot` | Visual verification only when absolutely needed | Low (size limits) |
</inspection_priority>

</essential_principles>

<intake>
What would you like to do?

1. **Debug a page** - Inspect console errors, network requests, evaluate scripts
2. **Performance trace** - Record and analyze page performance
3. **Automate interactions** - Fill forms, click elements, navigate pages
4. **Inspect page** - Get DOM structure, extract data, take snapshots

**Wait for response before proceeding.**
</intake>

<routing>
| Response | Workflow |
|----------|----------|
| 1, "debug", "console", "network", "errors" | [debug-page.md](./workflows/debug-page.md) |
| 2, "performance", "trace", "profile", "speed" | [performance-trace.md](./workflows/performance-trace.md) |
| 3, "automate", "fill", "click", "form", "interact" | [automate-page.md](./workflows/automate-page.md) |
| 4, "inspect", "snapshot", "extract", "DOM" | [inspect-page.md](./workflows/inspect-page.md) |
| Connection error | [start-chrome.md](./workflows/start-chrome.md) |

**After reading the workflow, follow it exactly.**
</routing>

<reference_index>
Domain knowledge in `references/`:

**Tools:** [tool-catalog.md](./references/tool-catalog.md) - Complete reference of all 26 MCP tools with parameters
</reference_index>

<workflows_index>
| Workflow | Purpose |
|----------|---------|
| [start-chrome.md](./workflows/start-chrome.md) | Start Chrome with debug profile on connection failure |
| [debug-page.md](./workflows/debug-page.md) | Inspect console errors, network issues, run script evaluation |
| [performance-trace.md](./workflows/performance-trace.md) | Record traces, analyze metrics, get performance insights |
| [automate-page.md](./workflows/automate-page.md) | Fill forms, click elements, handle dialogs, upload files |
| [inspect-page.md](./workflows/inspect-page.md) | Get DOM snapshots, extract data, take screenshots |
</workflows_index>

<success_criteria>
Task is complete when:
- Chrome running with debug profile at `~/GitHub/chrome-debug-profile`
- New tab opened (never navigated in existing tab)
- Requested operation executed using appropriate tools
- Results reported to user concisely
</success_criteria>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ondrejdrapalik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
