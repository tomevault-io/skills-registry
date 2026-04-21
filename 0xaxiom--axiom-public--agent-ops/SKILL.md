---
name: agent-ops
description: Workflow orchestration, sub-agent architecture, and task management patterns Use when this capability is needed.
metadata:
  author: 0xaxiom
---

# Agent Ops Skill

You are an orchestration-aware agent. This skill defines the operational patterns you follow for task management, workflow orchestration, and sub-agent coordination. These patterns ensure quality execution and continuous improvement.

---

## 🎯 TASK ORCHESTRATION

### Plan Mode Default
- **Enter plan mode for ANY non-trivial task** (3+ steps or architectural decisions)
- Write plan to `tasks/todo.md` with checkable items before starting
- If something goes sideways, **STOP and re-plan immediately** — don't keep pushing
- Check in with human before implementing if scope is large

### Task Flow (Always Follow This Order)
1. **Plan First**: Write structured plan to `tasks/todo.md`
2. **Verify Plan**: Check in before starting implementation  
3. **Track Progress**: Mark items complete as you go
4. **Verify Before Done**: Test it, prove it works, capture output
5. **Document Results**: Add review section to `tasks/todo.md`
6. **Capture Lessons**: Update `tasks/lessons.md` after ANY correction

### todo.md Template
```markdown
## Task: [Title]

**Goal:** [One sentence]
**Context:** [Why this matters]

### Plan
- [ ] Step 1
- [ ] Step 2
- [ ] Step 3

### Progress
*(Update as you go)*

### Verification
- [ ] Tested/proven it works
- [ ] Diffed against expected behavior
- [ ] "Would a staff engineer approve?"
- [ ] Logs/output captured

### Review
*(Add after completion: what worked, what didn't, lessons learned)*
```

---

## 🤖 SUB-AGENT COORDINATION

### When to Use Sub-Agents
- **Use sub-agents liberally** to keep main context window clean
- **Offload research, exploration, and parallel analysis** to sub-agents
- **One task per sub-agent** for focused execution
- For complex problems, throw more compute at it via multiple sub-agents

### Automatic Routing (Keyword Detection)
Route tasks based on keywords in user input:

| Keywords | Route To | Examples |
|----------|----------|----------|
| research, analyze, look up, find out, explore, documentation | scout | "Research x402 payments" |
| build, create skill, write script, implement, fix bug, debug | builder | "Create a price alert skill" |
| monitor, check price, LP status, scan mentions, health check | watcher | "Check LP position health" |
| write essay, compose, draft, blog post, story, tweet | writer | "Draft a thread about DeFi" |
| portfolio, transaction, wallet, token analysis, defi, on-chain | analyst | "Analyze this wallet" |
| clean, organize, archive, maintenance | archivist | "Clean up old log files" |
| complex, multi-step, ambiguous, needs judgment | main (keep) | "Help me decide..." |

### Explicit Routing (Override Keywords)
Use @mention syntax to directly route:
- `@scout: [task]` → Scout handles it
- `@builder: [task]` → Builder handles it  
- `@watcher: [task]` → Watcher handles it
- `@writer: [task]` → Writer handles it
- `@analyst: [task]` → Analyst handles it
- `@archivist: [task]` → Archivist handles it

### Sub-Agent Identity Boundaries
**Critical:** Sub-agents are NOT you. They work FOR you.
- Sub-agents cannot post publicly or send external messages
- Sub-agents cannot access MEMORY.md or personal context
- Sub-agents cannot speak as the main agent
- Sub-agents have specific, limited roles

---

## 📋 VERIFICATION CHECKLIST

Before marking any task complete, verify:
- [ ] **Tested/proven it works** — ran the code, clicked the buttons, verified output
- [ ] **Diffed against expected behavior** — does it match the requirements?
- [ ] **"Would a staff engineer approve this?"** — meets professional standards?
- [ ] **Logs/output captured** — evidence that it works documented

### Examples of Good Verification
```markdown
✅ Tested the Discord bot
   - Posted test message: ✅ appeared in #test-channel
   - Price alert triggered: ✅ sent when ETH > $3000
   - Error handling: ✅ graceful failure on bad API response

✅ Code review standards met
   - Error handling for all API calls
   - Input validation on user data
   - Clear variable names and comments
   - No hardcoded secrets or magic numbers
```

---

## 📊 SHARED STATE COORDINATION

### Reading State
Always check `agents/state.json` before starting work:
```bash
cat agents/state.json | jq '.activeTasks'  # What's in progress?
cat agents/state.json | jq '.agentStatus'  # Who's busy?
```

### Updating State  
Update `agents/state.json` when:
- Starting a new task
- Completing a task
- Spawning a sub-agent
- Learning something relevant

```javascript
// Example state update
const state = JSON.parse(fs.readFileSync('agents/state.json'));
state.activeTasks['auth-system'] = {
  status: 'completed',
  assignee: 'builder',
  completed: new Date().toISOString()
};
fs.writeFileSync('agents/state.json', JSON.stringify(state, null, 2));
```

### State Structure
```json
{
  "activeTasks": {
    "task-name": {
      "status": "in-progress|completed|blocked",
      "assignee": "agent-label",
      "started": "ISO-timestamp",
      "description": "brief summary"
    }
  },
  "agentStatus": {
    "scout": { "status": "busy|idle", "lastTask": "task-name" },
    "builder": { "status": "idle", "lastTask": null }
  },
  "meta": {
    "version": 1,
    "updatedAt": "ISO-timestamp"
  }
}
```

---

## 🔄 SELF-CORRECTION LOOP

### Lessons Pattern
All correction rules live in `tasks/lessons.md`. Review at session start.

After ANY correction from human:
1. **Immediately add the pattern** to `tasks/lessons.md`
2. **Write it as a rule** that prevents the same mistake
3. **Be specific** — include the wrong way and the right way
4. **Iterate** until mistake rate drops

### Lessons Template
```markdown
## Tool-Specific Rules

### Twitter API
- **Always pass `--reply-to <tweet_id>`** when replying — otherwise posts as standalone
- **Never use `bird tweet` to read** — it posts, not reads

### Publishing
- **NEVER publish without approval** — includes npm, tweets, posts
- Test APIs with GET requests, not by creating public content
```

### Example Lesson Addition
```markdown
## Delegation Patterns

### Sub-Agent Spawning
- **Always check agent status before spawning** — don't create duplicate work
- **Use explicit task descriptions** — vague tasks lead to vague results
- **Include workspace paths** — sub-agents need context about where to work

*Added after: Scout was spawned twice for same research task*
```

---

## 🛠️ SCRIPT USAGE

### Initialize Agent Ops
```bash
bash skills/agent-ops/scripts/init.sh
```
Creates: `tasks/todo.md`, `tasks/lessons.md`, `agents/registry.json`, `agents/state.json`, `tasks/archive/`

### Spawn Sub-Agent
```bash
node skills/agent-ops/scripts/spawn.mjs <agent-name> "<task-description>"

# Examples:
node skills/agent-ops/scripts/spawn.mjs scout "Research Uniswap V4 hooks"
node skills/agent-ops/scripts/spawn.mjs builder "Create Discord price bot"
```

---

## 📁 FILE ORGANIZATION

### Required Files (Create if Missing)
- `tasks/todo.md` — Current task tracking
- `tasks/lessons.md` — Self-correction patterns  
- `agents/registry.json` — Sub-agent definitions
- `agents/state.json` — Shared coordination state
- `tasks/archive/` — Completed task storage

### Optional Files
- `HEARTBEAT.md` — Proactive check reminders
- `memory/heartbeat-state.json` — Track periodic checks
- `tasks/backlog.md` — Future task ideas

### Archive Pattern
When tasks complete, move to archive:
```bash
mv tasks/todo.md tasks/archive/$(date +%Y-%m-%d)-task-name.md
cp skills/agent-ops/references/todo-template.md tasks/todo.md
```

---

## 🎭 DELEGATION EXAMPLES

### Simple Research Task
```
Input: "How does Uniswap V4's hook system work?"
→ Auto-routes to Scout based on "research" keyword
→ Scout researches and reports findings
→ Main agent summarizes for human
```

### Complex Multi-Agent Task  
```
Input: "Build a DeFi yield tracker for our positions"
→ Main agent enters plan mode:
   1. @scout: Research yield farming protocols on Base
   2. @analyst: Analyze current DeFi positions
   3. @builder: Create tracking dashboard
   4. @watcher: Add health monitoring
→ Coordinates via state.json
→ Main agent reviews final integration
```

### Explicit Override
```
Input: "@builder: Fix the bug in the price alert system"
→ Ignores keyword routing
→ Directly assigns to Builder
→ Builder debugs and reports fix
```

---

## ⚠️ QUALITY GATES

### Before Task Completion
1. **Verification checklist passed** — all items checked
2. **Output captured** — logs, screenshots, test results  
3. **State updated** — `agents/state.json` reflects completion
4. **Documentation updated** — README, comments, or relevant docs
5. **Lessons captured** — if any corrections were made

### Before Sub-Agent Delegation
1. **Task is well-defined** — clear deliverables and constraints
2. **Agent capability matches** — right agent for the job
3. **State checked** — not duplicating existing work
4. **Workspace specified** — agent knows where to work

### Before Plan Execution
1. **Human approval** — for large scope or risky changes
2. **Dependencies resolved** — required tools/access available
3. **Success criteria clear** — how will we know it's done?
4. **Rollback plan** — what if something breaks?

---

This skill enables systematic, high-quality task execution through proven orchestration patterns. Use these guidelines to coordinate complex workflows and continuously improve through structured learning.
---

## 🎯 MISSION CONTROL PATTERNS (ADVANCED)

For production-grade multi-agent coordination, see `references/patterns.md` for:

- **Heartbeat Staggering**: Prevent rate limits by offsetting cron jobs
- **Enhanced state.json**: Tasks, notifications, activities schema
- **Task Lifecycle**: Inbox→Assigned→In Progress→Review→Done
- **Working Memory**: WORKING.md for session continuity
- **@Mentions**: Agent-to-agent notification system
- **Daily Standups**: Automated progress reports
- **Cost Optimization**: Model selection by task type

**Quick wins to implement:**
1. Stagger your cron jobs (5-10 min offsets)
2. Add WORKING.md to each agent's workspace
3. Track tasks in state.json with full lifecycle
4. Use Haiku for monitoring, Opus for creative work

The goal: Turn independent agents into a coordinated team.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0xaxiom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
