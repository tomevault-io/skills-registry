---
name: orchestrate
description: Spawn parallel worker terminals to execute multi-domain tasks concurrently. Use when 3+ independent tasks span 2+ domains (frontend, backend, testing, security, etc.) Use when this capability is needed.
metadata:
  author: lofidonut3
---

# /orchestrate - Parallel Worker Orchestration

## Overview

Execute complex multi-domain tasks in parallel by spawning specialized worker terminals.
Each worker runs an independent OpenCode instance focused on a single domain.

**Core principle:** 1 domain = 1 worker terminal. All workers execute concurrently.

## ⚠️ Depth Limit (Recursive Orchestration Safety)

**Maximum orchestration depth: 2**

```
Main Orchestrator (depth=0)
    │
    ├── Worker 1 (depth=1) ── can spawn sub-workers
    │       │
    │       └── Sub-worker 1-1 (depth=2) ── CANNOT spawn (max reached)
    │
    └── Worker 2 (depth=1) ── can spawn sub-workers
            │
            └── Sub-worker 2-1 (depth=2) ── CANNOT spawn (max reached)
```

**Environment Variables:**
- `ORCH_DEPTH`: Current orchestration depth (0=Main, 1=Worker, 2=Sub-worker)
- `ORCH_MAX_DEPTH`: Maximum allowed depth (default: 2)

**Behavior at MAX_DEPTH:**
- Worker executes tasks sequentially instead of spawning sub-workers
- Logs warning: "Max depth reached, no worker spawning allowed"
- Returns results in `mode: sequential` format

**Why depth=2?**
- Depth 1 (Worker): Essential for parallel task execution
- Depth 2 (Sub-worker): Useful when Worker has complex sub-tasks to split
- Depth 3+: No practical benefit, coordination overhead exceeds gains

## ⚠️ Windows Support via WSL

**On Windows, this skill uses WSL (Windows Subsystem for Linux) + tmux.**

### Prerequisites (Windows)
- WSL installed with Ubuntu: `wsl --install -d Ubuntu`
- tmux in WSL: `sudo apt install tmux`
- Node.js in WSL: `sudo apt install nodejs npm`
- OpenCode in WSL: `sudo npm install -g opencode-ai`
- Skills symlinked: `ln -sf /mnt/c/Users/<USER>/.claude/skills ~/.claude/skills`
- CLAUDE.md symlinked: `ln -sf /mnt/c/Users/<USER>/.claude/CLAUDE.md ~/.claude/CLAUDE.md`

### Windows Spawning Command (Visible - Recommended)
```bash
# Opens a NEW Windows Terminal tab for each worker (visible):
wt -w 0 new-tab --title "omo-worker-NAME" wsl -d Ubuntu -- bash -c "cd /mnt/c/path/to/project && opencode run 'TASK'; echo 'Press Enter to close'; read"
```

### Windows Spawning Command (Background - tmux)
```bash
# Runs in background via tmux (not visible, use tmux attach to see):
wsl -d Ubuntu -- bash -c "cd /mnt/c/path/to/project && tmux new-session -d -s omo-worker-NAME 'opencode run \"TASK\"'"
```

## When to Use

```
Multiple independent tasks? ────► Are they 3+ tasks? ────► Do they span 2+ domains?
         │                              │                          │
         ▼                              ▼                          ▼
        No                             No                         Yes
         │                              │                          │
         ▼                              ▼                          ▼
   Single execution            Single execution             /orchestrate
```

**USE when:**
- 3+ tasks that can run independently
- Tasks span 2+ domains (frontend + backend + testing...)
- No shared state or sequential dependencies
- Estimated time > 20 minutes if done sequentially

**DON'T USE when:**
- Tasks are interdependent (must run in order)
- Single domain work only
- Quick tasks (< 5 min each)
- Need to see results before next step

## Usage

```
/orchestrate <task description>
```

### Examples

```bash
# Basic usage
/orchestrate Build login UI, Create JWT API, Write tests, Security audit

# From speckit tasks
/orchestrate execute tasks from .opencode/plans/user-auth-plan.md

# Explicit domains
/orchestrate frontend:Build login page | backend:Create API | testing:Write E2E tests
```

## How It Works

### Step 1: Task Analysis

The orchestrator analyzes your tasks and auto-detects domains:

```
Input: "Build login UI, Create JWT API, Write tests, Security audit"

Analysis:
  - "Build login UI" → frontend (keywords: UI, login, component)
  - "Create JWT API" → backend (keywords: API, JWT, endpoint)
  - "Write tests" → testing (keywords: tests, E2E)
  - "Security audit" → security (keywords: security, audit)

Result: 4 domains → 4 workers
```

### Step 2: Worker Spawning

For each domain, a tmux terminal is spawned:

**Linux/Mac:**
```bash
# Automatically executed:
tmux new-session -d -s omo-worker-frontend
tmux new-session -d -s omo-worker-backend
tmux new-session -d -s omo-worker-testing
tmux new-session -d -s omo-worker-security

# Each session runs OpenCode with domain-specific tasks
```

**Windows (via WSL):**
```bash
# Automatically executed:
wsl -d Ubuntu -- bash -c "cd /mnt/c/path/to/project && tmux new-session -d -s omo-worker-frontend 'opencode run \"frontend tasks\"'"
wsl -d Ubuntu -- bash -c "cd /mnt/c/path/to/project && tmux new-session -d -s omo-worker-backend 'opencode run \"backend tasks\"'"
# ... etc
```

### Step 3: Parallel Execution

Workers execute in parallel. Default behavior:

- **Opencode workers** are spawned when available
- **Fallback**: prompt files are created for manual execution

```
┌─────────────────────────────────────────────────────────┐
│                    MAIN ORCHESTRATOR                    │
│                   (this terminal)                       │
└─────────────────────────────────────────────────────────┘
          │                │                │
          ▼                ▼                ▼
┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│omo-frontend │  │omo-backend  │  │omo-testing  │
│             │  │             │  │             │
│ Build UI    │  │ Create API  │  │ Write tests │
│ Form valid  │  │ JWT auth    │  │ E2E specs   │
└─────────────┘  └─────────────┘  └─────────────┘
```

### Step 4: Result Collection

Workers write results to shared workspace:

```
~/.opencode-orchestrator/
├── worker-frontend_result.json
├── worker-backend_result.json
├── worker-testing_result.json
└── event_log.jsonl
```

Main orchestrator watches result files and event bus, then reports:

```
[COMPLETE] All 4 workers finished
  - frontend: 3/3 tasks ✓
  - backend: 2/2 tasks ✓
  - testing: 2/2 tasks ✓
  - security: 1/1 tasks ✓

Total time: 18 minutes (vs ~60 min sequential)
```

## Supported Domains

### Default Domains (auto-detected)

| Domain | Keywords |
|--------|----------|
| frontend | ui, ux, component, react, vue, css, tailwind, form |
| backend | api, endpoint, server, jwt, oauth, middleware |
| testing | test, spec, coverage, jest, pytest, e2e |
| database | sql, migration, schema, orm, postgres, mongodb |
| devops | docker, kubernetes, ci/cd, deploy, terraform |
| security | audit, penetration, vulnerability, owasp, encryption |
| ml | model, training, dataset, neural network, pytorch |
| mobile | ios, android, react native, flutter |
| documentation | docs, readme, api docs, guide |

### Custom Domains

Register new domains at runtime:

```python
# In your task description:
/orchestrate blockchain:Deploy smart contract | defi:Build liquidity pool
```

Or programmatically:

```python
from domain_registry import DomainRegistry
DomainRegistry.register("blockchain", ["smart contract", "web3", "ethereum"])
```

### Dependencies (DAG)

Add task IDs and dependencies inline:

```
/orchestrate \
  "Set up database (id: db)" \
  "Create API (id: api) (depends_on: db)" \
  "Build UI (id: ui) (depends_on: api)"
```

Tasks with unmet dependencies are held until prerequisites complete.

## Worker Monitoring

### View all workers

**Linux/Mac:**
```bash
tmux list-sessions | grep omo-
```

**Windows (via WSL):**
```bash
wsl -d Ubuntu -- tmux list-sessions | grep omo-
```

### Attach to specific worker

**Linux/Mac:**
```bash
tmux attach -t omo-worker-frontend
# Detach: Ctrl+B, D
```

**Windows (via WSL):**
```bash
wsl -d Ubuntu -- tmux attach -t omo-worker-frontend
# Or open WSL terminal first, then: tmux attach -t omo-worker-frontend
```

### Kill all workers

**Linux/Mac:**
```bash
tmux kill-session -t omo-worker-frontend
# Or all at once:
/orchestrate --kill-all
```

**Windows (via WSL):**
```bash
wsl -d Ubuntu -- tmux kill-server
# Or specific session:
wsl -d Ubuntu -- tmux kill-session -t omo-worker-frontend
```

### Fallback mode (no tmux/WSL)
If tmux/opencode isn't available, prompt files are created in:
```
~/.opencode-orchestrator/
```
Run manually:
```bash
opencode < ~/.opencode-orchestrator/worker-frontend_prompt.md
```

## Integration with Speckit Workflow

When working in a speckit project:

```
/orchestrate execute .opencode/plans/user-auth-plan.md
```

The orchestrator will:
1. Read the plan file
2. Parse tasks with `[Skill: name]` tags
3. Group by domain
4. Execute in parallel
5. Update spec status on completion

## Implementation Details

### Files Location

```
~/.claude/multi-agent-orchestrator/
├── src/
│   ├── opencode_integration.py   # Main integration
│   ├── domain_registry.py        # Dynamic domains
│   ├── orchestrator.py           # Core logic
│   ├── event_bus.py              # Inter-worker communication
│   └── worker.py                 # Worker process
├── bin/
│   ├── opencode-orch             # Linux/Mac wrapper
│   └── opencode-orch.bat         # Windows wrapper
└── SKILL.md                      # (copy of this file)
```

### Python API (for advanced use)

```python
from opencode_integration import OpenCodeOrchestrator, WorkerConfig

orch = OpenCodeOrchestrator()

workers = [
    WorkerConfig(name="frontend", domain="frontend", tasks=["Build login UI"]),
    WorkerConfig(name="backend", domain="backend", tasks=["Create JWT API"]),
]

result = orch.spawn_workers(workers)
print(result)  # {"spawned": ["frontend", "backend"], "failed": []}
```

## Heuristics for Auto-Activation

Sisyphus can auto-invoke this skill when detecting:

```python
def should_orchestrate(tasks):
    # 3+ independent tasks
    if len(tasks) < 3:
        return False
    
    # 2+ different domains
    domains = detect_domains(tasks)
    if len(set(domains)) < 2:
        return False
    
    # No sequential dependencies
    if has_dependencies(tasks):
        return False
    
    return True
```

## Common Patterns

### Pattern 1: Full-Stack Feature
```
/orchestrate Build user registration UI, Create registration API, Add E2E tests
```

### Pattern 2: Security Audit
```
/orchestrate Run OWASP scan, Check dependencies for vulnerabilities, Audit authentication flow
```

### Pattern 3: Refactoring
```
/orchestrate Refactor frontend components, Update API contracts, Fix broken tests
```

### Pattern 4: From Plan File
```
/orchestrate execute .opencode/plans/payment-system-plan.md
```

## Troubleshooting

### Workers not spawning

**Linux/Mac:**
```bash
# Check tmux is installed
which tmux

# If not, install
sudo apt install tmux  # Linux
brew install tmux      # Mac
```

**Windows:**
```bash
# Check WSL is available
wsl --list --verbose

# Check tmux in WSL
wsl -d Ubuntu -- which tmux

# If tmux missing in WSL:
wsl -d Ubuntu -- sudo apt install tmux

# Check opencode in WSL:
wsl -d Ubuntu -- opencode --version

# If opencode missing:
wsl -d Ubuntu -- sudo npm install -g opencode-ai

# Check skills are symlinked:
wsl -d Ubuntu -- ls ~/.claude/skills/
```

### Workers stuck
```bash
# Attach and check
tmux attach -t omo-worker-frontend

# Kill and retry
/orchestrate --kill-all
/orchestrate <tasks again>
```

### Results not collected
```bash
# Check workspace
ls ~/.opencode-orchestrator/

# Check event log
tail -f ~/.opencode-orchestrator/event_log.jsonl
```

## Performance Comparison

| Scenario | Sequential | Parallel (3 workers) | Speedup |
|----------|------------|----------------------|---------|
| Simple CRUD | 15 min | 6 min | 2.5x |
| Auth System | 45 min | 18 min | 2.5x |
| Full-Stack App | 90 min | 35 min | 2.6x |

## Best Practices

1. **Group related tasks** - Don't split tightly coupled work
2. **Keep domains balanced** - Avoid 10 tasks in one domain, 1 in another
3. **Monitor long-running workers** - Attach if something seems stuck
4. **Review before merge** - Workers work independently, verify integration
5. **Start small** - Test with 2-3 workers before scaling up

---

## Interactive Mode (Event-Based Bidirectional Communication)

**Scenario**: Main Sisyphus acts as the "user" for a Worker agent.

```
User: "Build whatever you want"
        │
        ▼
Main Sisyphus
        │
        ├── Decide what to build (e.g., "AI Code Review Bot")
        │
        └── Spawn Worker (runs oh-my-speckit)
                │
                ├── Phase 0-6: Main ↔ Worker conversation
                │       Worker asks → Stop hook fires → Event written
                │       → Main detects event → Main responds
                │
                └── Phase 7-10: Worker self-orchestrates
                        Worker → /orchestrate → Sub-workers
```

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         MAIN SISYPHUS                            │
│                                                                   │
│  1. Spawn Worker in tmux with ORCH_WORKER_NAME=interactive       │
│  2. inotifywait on ~/.opencode-orchestrator/events/              │
│  3. On event: read terminal_content, generate response           │
│  4. Send response via: tmux send-keys -t omo-worker-interactive  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ Events (file watch)
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│  ~/.opencode-orchestrator/events/interactive.event               │
│                                                                   │
│  {                                                                │
│    "type": "worker_stopped",                                      │
│    "worker_name": "interactive",                                  │
│    "session_id": "ses_xxx",                                       │
│    "timestamp": "...",                                            │
│    "terminal_content": "..."  ← Worker's last output              │
│  }                                                                │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ Stop hook writes
                              │
┌─────────────────────────────────────────────────────────────────┐
│                        WORKER (tmux)                             │
│  Session: omo-worker-interactive                                 │
│  Env: ORCH_WORKER_NAME=interactive                               │
│                                                                   │
│  Running: opencode (with Stop hook active)                       │
│  → Worker completes response → Stop hook fires                   │
│  → Event file written → Main detects and responds                │
└─────────────────────────────────────────────────────────────────┘
```

### Execution Protocol (MANDATORY)

**Step 1: Spawn Worker with env var**
```bash
WORKER_NAME="interactive"
TMUX_SESSION="omo-worker-${WORKER_NAME}"

# Spawn worker with ORCH_WORKER_NAME set
tmux new-session -d -s "$TMUX_SESSION" \
  "ORCH_WORKER_NAME=$WORKER_NAME opencode"

# Send initial task
INITIAL_PROMPT="Build an AI-powered code review bot. Start with /speckit.specify"
tmux send-keys -t "$TMUX_SESSION" "$INITIAL_PROMPT" Enter
```

**Step 2: Watch for events (in background)**
```bash
EVENT_DIR="$HOME/.opencode-orchestrator/events"
mkdir -p "$EVENT_DIR"

# This loop runs until Worker completes or timeout
while true; do
  # Wait for event (blocks until file change or timeout)
  EVENT=$(~/.local/bin/orchestrator-event-watcher.sh "$WORKER_NAME" 300)
  
  if echo "$EVENT" | jq -e '.type == "timeout"' > /dev/null; then
    echo "Worker timed out"
    break
  fi
  
  # Extract terminal content
  TERMINAL_CONTENT=$(echo "$EVENT" | jq -r '.terminal_content')
  
  # Process and generate response (Main's decision)
  # ... Main analyzes TERMINAL_CONTENT and decides response ...
  
  RESPONSE="Your response here based on worker's question"
  
  # Send response to Worker
  tmux send-keys -t "$TMUX_SESSION" "$RESPONSE" Enter
done
```

**Step 3: Detect completion**
```bash
# Check terminal content for completion signals
if echo "$TERMINAL_CONTENT" | grep -q "Implementation complete\|All tasks completed"; then
  echo "Worker finished"
  tmux kill-session -t "$TMUX_SESSION"
fi
```

### Main's Decision Logic

When Main receives Worker output, analyze for:

| Pattern | Worker State | Main Action |
|---------|--------------|-------------|
| Question mark at end | Asking clarification | Answer the question |
| "Option 1/2/3" list | Offering choices | Pick preferred option |
| "Should I proceed?" | Confirmation request | "Yes" or "No with reason" |
| Spec/plan presented | Review request | Approve or request changes |
| "Implementation complete" | Finished | End conversation |
| Error/stuck | Problem | Help resolve or adjust scope |

### Example: Full Interactive Session

```bash
# Main spawns Worker for autonomous development
WORKER_NAME="autonomous-dev"
tmux new-session -d -s "omo-worker-$WORKER_NAME" \
  "ORCH_WORKER_NAME=$WORKER_NAME opencode"

# Initial prompt: build whatever Main wants
tmux send-keys -t "omo-worker-$WORKER_NAME" \
  "Build a CLI tool for managing Claude Code skills. Include: list, create, edit, validate commands. Use /speckit.specify to start." Enter

# Event loop
while true; do
  EVENT=$(~/.local/bin/orchestrator-event-watcher.sh "$WORKER_NAME" 300)
  
  if echo "$EVENT" | jq -e '.type == "timeout"' > /dev/null; then
    break
  fi
  
  CONTENT=$(echo "$EVENT" | jq -r '.terminal_content')
  
  # Main analyzes Worker's question and responds
  # Example: Worker asks "Should the tool support plugins?"
  # Main responds based on its vision for the project
  
  RESPONSE="Yes, support plugins. Use a ~/.skill-manager/plugins/ directory."
  tmux send-keys -t "omo-worker-$WORKER_NAME" "$RESPONSE" Enter
done
```

### Files Created

| File | Purpose |
|------|---------|
| `~/.local/bin/worker-stop-event.sh` | Stop hook - writes event when Worker completes |
| `~/.local/bin/orchestrator-event-watcher.sh` | inotifywait wrapper for Main |
| `~/.opencode-orchestrator/events/*.event` | Event files (one per worker) |
| `~/.opencode-orchestrator/event_log.jsonl` | Debug log of all events |

### When to Use Interactive Mode

- **"Build whatever you want"** - autonomous development requests
- Main needs to guide Worker's spec/design/plan decisions
- Phase 0-6 requires human-like judgment that Main provides

### When NOT to Use

- Clear tasks already defined → Use regular `/orchestrate`
- Only Phase 7-10 execution needed → Use parallel workers mode

---

## 👉 EXECUTION PROTOCOL

When this skill is invoked, **IMMEDIATELY execute** the following:

### Option A: Use Python Orchestrator (RECOMMENDED)

For full features (dependency handling, skill mapping, pub/sub):

```bash
cd ~/.claude/multi-agent-orchestrator/src && python -c "
import sys
sys.path.insert(0, '.')
from orchestrator import MultiAgentOrchestrator

tasks = '''
YOUR_TASKS_HERE
'''

orch = MultiAgentOrchestrator(workspace='~/.opencode-orchestrator')
result = orch.orchestrate(tasks, use_tmux=True, use_opencode_workers=True)
print(result)
"
```

**Features:**
- Automatic skill tag parsing: `[Skill: name]` or `→ /skill-name`
- Dependency-based execution: `(id: X)`, `(depends_on: A,B)`
- Pub/Sub event bus for worker communication
- Automatic re-spawning when dependencies resolve

### Option B: Simple Bash Spawning (Quick)

For simple parallel execution without dependency handling:

### Step 1: Detect Platform
```bash
uname -s  # "MINGW64_NT-*" = Windows, "Linux" = Linux, "Darwin" = Mac
```

### Step 2: Spawn Workers (Windows - Visible Tabs)

**For each task/domain:**

**2a. Create task script in WSL:**
```bash
wsl -d Ubuntu -- bash << 'WSLEOF'
cat > ~/.omo/task-DOMAIN.sh << 'TASKEOF'
#!/bin/bash
cd /mnt/c/code/PROJECT_PATH
echo "=== Worker: DOMAIN ==="
echo "Task: TASK_DESCRIPTION"
echo ""
opencode run "TASK_DESCRIPTION"
echo ""
echo "=== Complete ==="
read -p "Press Enter to close..."
TASKEOF
chmod +x ~/.omo/task-DOMAIN.sh
WSLEOF
```

**2b. Open new Windows Terminal tab:**
```bash
cmd //c "wt -w 0 new-tab --title omo-DOMAIN -- wsl -d Ubuntu bash -c /home/USER/.omo/task-DOMAIN.sh"
```

**IMPORTANT:** Use `cmd //c` to avoid Git Bash path conversion issues.

### Step 2 Alternative: Linux/Mac
```bash
tmux new-session -d -s omo-worker-DOMAIN "opencode run 'TASK_DESCRIPTION' 2>&1 | tee /tmp/omo-DOMAIN.log"
```

### Step 3: Monitor (Windows)
Workers are visible in separate tabs. Check tab titles: `omo-frontend`, `omo-backend`, etc.

### Step 4: Cleanup
```bash
# Windows - scripts cleanup:
wsl -d Ubuntu -- rm ~/.omo/task-*.sh

# Linux/Mac - tmux cleanup:
tmux kill-server
```

### Example: Spawn 3 Workers in Parallel

```bash
# 1. Create scripts
wsl -d Ubuntu -- bash << 'EOF'
cat > ~/.omo/task-frontend.sh << 'SCRIPT'
#!/bin/bash
cd /mnt/c/code/opencode_project
echo "=== Worker: frontend ===" && opencode run "Build login UI component"
echo "=== Complete ===" && read -p "Press Enter..."
SCRIPT
chmod +x ~/.omo/task-frontend.sh

cat > ~/.omo/task-backend.sh << 'SCRIPT'
#!/bin/bash
cd /mnt/c/code/opencode_project
echo "=== Worker: backend ===" && opencode run "Create authentication API"
echo "=== Complete ===" && read -p "Press Enter..."
SCRIPT
chmod +x ~/.omo/task-backend.sh

cat > ~/.omo/task-testing.sh << 'SCRIPT'
#!/bin/bash
cd /mnt/c/code/opencode_project
echo "=== Worker: testing ===" && opencode run "Write E2E tests for auth flow"
echo "=== Complete ===" && read -p "Press Enter..."
SCRIPT
chmod +x ~/.omo/task-testing.sh
EOF

# 2. Spawn all workers (3 new tabs open simultaneously)
cmd //c "wt -w 0 new-tab --title omo-frontend -- wsl -d Ubuntu bash -c /home/USER/.omo/task-frontend.sh"
cmd //c "wt -w 0 new-tab --title omo-backend -- wsl -d Ubuntu bash -c /home/USER/.omo/task-backend.sh"
cmd //c "wt -w 0 new-tab --title omo-testing -- wsl -d Ubuntu bash -c /home/USER/.omo/task-testing.sh"
```

**DO NOT explain. EXECUTE the worker spawning commands immediately.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lofidonut3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
