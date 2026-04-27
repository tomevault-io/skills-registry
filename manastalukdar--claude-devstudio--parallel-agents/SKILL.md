---
name: parallel-agents
description: Manage multiple Claude instances for complex tasks with coordination Use when this capability is needed.
metadata:
  author: manastalukdar
---

# Parallel Agent Orchestration

I'll help you coordinate multiple Claude instances to tackle complex tasks through parallel execution and intelligent task distribution.

Arguments: `$ARGUMENTS` - task description, number of agents, or coordination mode

## Token Optimization

This skill uses multiple optimization strategies to minimize token usage while maintaining comprehensive multi-agent coordination:

### 1. Orchestration State Caching (1,500 token savings)

**Pattern:** Maintain persistent state files to avoid re-analyzing task structure

```bash
# Cache file: parallel-agents/state.json
# Format: JSON with task breakdown, agent assignments, progress
# TTL: Session-based (persists until task complete)

if [ -f "parallel-agents/state.json" ]; then
    # Read cached orchestration (200 tokens)
    STATE=$(cat parallel-agents/state.json)
    AGENTS=$(echo "$STATE" | jq -r '.agents[]')
    PROGRESS=$(echo "$STATE" | jq -r '.progress')
else
    # Full task decomposition (1,700 tokens)
    analyze_task_structure
    create_agent_assignments
    save_state
fi
```

**Savings:**
- Cached state: ~200 tokens (JSON read + parse)
- Full decomposition: ~1,700 tokens (codebase analysis + task breakdown)
- **1,500 token savings (88%)** for resumed sessions

### 2. Early Exit for Sequential Work (95% savings)

**Pattern:** Detect tasks unsuitable for parallelization immediately

```bash
# Quick heuristics check (300 tokens)
file_count=$(find . -type f \( -name "*.js" -o -name "*.ts" -o -name "*.py" \) | wc -l)

if [ "$file_count" -lt 10 ]; then
    echo "⚠️  Small codebase ($file_count files) - parallel agents not beneficial"
    echo "   Recommendation: Use single agent for this task"
    exit 0  # 300 tokens total
fi

# Check for tightly coupled changes
if [[ "$ARGUMENTS" =~ "single file"|"refactor function"|"fix bug" ]]; then
    echo "⚠️  Task appears sequential - single agent recommended"
    exit 0  # 300 tokens total
fi

# Otherwise: Full multi-agent orchestration (4,000+ tokens)
```

**Savings:**
- Sequential work detected: ~300 tokens (early exit)
- Full orchestration: ~4,000+ tokens
- **3,700+ token savings (92%)** when parallelization not suitable

### 3. Agent Work Package Templates (2,000 token savings)

**Pattern:** Use predefined templates for agent instructions instead of LLM generation

```bash
# Instead of: LLM-generated agent instructions (2,500 tokens per agent)
# Use: Template-based work packages (500 tokens per agent)

generate_agent_package() {
    local agent_role="$1"
    local work_scope="$2"

    cat > "parallel-agents/agents/$agent_role/instructions.md" <<EOF
# Agent: $agent_role
## Work Scope
$work_scope

## Coordination
- Sync point: After phase completion
- Communication: See ../sync/messages.md
- Branch: feature/$agent_role-work

## Dependencies
[Auto-generated from dependency graph]

## Success Criteria
[Derived from master plan]
EOF
}
```

**Savings:**
- Template-based: ~500 tokens per agent (5 agents = 2,500 tokens)
- LLM-generated: ~2,500 tokens per agent (5 agents = 12,500 tokens)
- **2,000 token savings (80%)** per agent × 5 agents = **10,000 token savings**

### 4. Progressive Agent Activation (70% savings)

**Pattern:** Start with minimal agents, scale up only if needed

```bash
# Instead of: Always spin up 5 agents (15,000+ tokens)
# Start with: Analyze and scale progressively (2,000-8,000 tokens)

determine_agent_count() {
    local module_count=$(find src -maxdepth 1 -type d | wc -l)
    local complexity_score=$((file_count / 100 + module_count))

    if [ "$complexity_score" -lt 5 ]; then
        echo "2"  # Small task: 2 agents (4,000 tokens)
    elif [ "$complexity_score" -lt 15 ]; then
        echo "3"  # Medium task: 3 agents (7,000 tokens)
    else
        echo "5"  # Large task: 5 agents (15,000 tokens)
    fi
}
```

**Savings:**
- Small task (2 agents): ~4,000 tokens
- Large task (5 agents): ~15,000 tokens
- **Average 70% savings** by right-sizing agent count

### 5. Dependency Graph Caching (800 token savings)

**Pattern:** Cache task dependency analysis to avoid recomputation

```bash
# Cache file: parallel-agents/dependency-graph.json
# Format: JSON with task nodes and edges
# TTL: Session-based (until task structure changes)

if [ -f "parallel-agents/dependency-graph.json" ]; then
    # Read cached graph (100 tokens)
    DEP_GRAPH=$(cat parallel-agents/dependency-graph.json)
else
    # Build dependency graph (900 tokens)
    analyze_task_dependencies
    identify_critical_path
    determine_parallelizable_work
    save_dependency_graph
fi
```

**Savings:**
- Cached graph: ~100 tokens
- Fresh analysis: ~900 tokens (codebase analysis + dependency detection)
- **800 token savings (89%)** for resumed/updated coordination

### 6. Bash-Based Project Analysis (1,200 token savings)

**Pattern:** Use bash commands for codebase analysis instead of file reads

```bash
# Instead of: Read files to count complexity (2,000+ tokens)
# Use: Bash commands for metrics (800 tokens)

analyze_codebase_bash() {
    # File counts by type
    local js_files=$(find . -name "*.js" -type f | wc -l)
    local py_files=$(find . -name "*.py" -type f | wc -l)
    local total_lines=$(find . -type f \( -name "*.js" -o -name "*.py" \) -exec wc -l {} + | tail -1 | awk '{print $1}')

    # Module structure
    local modules=$(find src -maxdepth 1 -type d | wc -l)

    # Dependencies
    local deps=$(jq -r '.dependencies | keys | length' package.json 2>/dev/null || echo "0")

    # Output summary (no file reads, just metrics)
    echo "Files: $js_files JS, $py_files Py"
    echo "Lines: $total_lines"
    echo "Modules: $modules"
    echo "Dependencies: $deps"
}
```

**Savings:**
- Bash analysis: ~800 tokens (commands + output)
- File-based analysis: ~2,000 tokens (read multiple files)
- **1,200 token savings (60%)**

### 7. Inter-Agent Communication Optimization (600 token savings)

**Pattern:** Use file-based signaling instead of verbose coordination

```bash
# Instead of: LLM-mediated communication (1,000 tokens per sync)
# Use: File-based signals (400 tokens per sync)

# Agent writes completion signal
mark_phase_complete() {
    local agent_id="$1"
    local phase="$2"
    echo "$phase" > "parallel-agents/sync/$agent_id-complete-$phase.signal"
}

# Orchestrator checks signals
check_sync_point() {
    local phase="$1"
    local agent_count="$2"
    local complete_count=$(ls parallel-agents/sync/*-complete-$phase.signal 2>/dev/null | wc -l)

    if [ "$complete_count" -eq "$agent_count" ]; then
        echo "✓ All agents completed $phase"
        return 0
    else
        echo "⏳ Waiting: $complete_count/$agent_count agents complete"
        return 1
    fi
}
```

**Savings:**
- File-based signaling: ~400 tokens (file operations)
- LLM coordination: ~1,000 tokens (natural language communication)
- **600 token savings (60%)** per sync point × multiple sync points

### 8. Real-World Token Usage Distribution

**Typical Scenarios:**

1. **Initial Setup - Large Parallel Task (8,000-12,000 tokens)**
   - Project analysis: 800 tokens (Bash-based)
   - Task decomposition: 1,700 tokens
   - Dependency graph: 900 tokens
   - 5 agent packages: 500 tokens/agent × 5 = 2,500 tokens
   - Coordination setup: 1,000 tokens
   - **Total: ~6,900 tokens**

2. **Resume Existing Orchestration (1,000-2,000 tokens)**
   - State loading (cached): 200 tokens
   - Dependency graph (cached): 100 tokens
   - Progress check: 300 tokens
   - Agent status: 400 tokens
   - **Total: ~1,000 tokens**

3. **Small Task - Early Exit (250-400 tokens)**
   - Quick analysis: 200 tokens
   - Sequential detection: 100 tokens
   - Recommendation: 100 tokens
   - **Total: ~400 tokens**

4. **Medium Task - 3 Agents (4,000-7,000 tokens)**
   - Project analysis: 800 tokens
   - Task decomposition: 1,700 tokens
   - 3 agent packages: 500 tokens/agent × 3 = 1,500 tokens
   - Coordination: 800 tokens
   - **Total: ~4,800 tokens**

5. **Sync Point Check (300-500 tokens)**
   - File-based signal check: 400 tokens
   - Progress report: 100 tokens
   - **Total: ~500 tokens**

**Expected Token Savings:**
- **Average 65% reduction** from baseline (12,000 → 4,200 tokens)
- **92% reduction** when parallelization not suitable (early exit)
- **88% reduction** for resumed orchestration sessions
- **Aggregate savings: 6,000-8,000 tokens** per multi-agent coordination

### Optimization Summary

| Strategy | Savings | When Applied |
|----------|---------|--------------|
| Orchestration state caching | 1,500 tokens (88%) | Resumed sessions |
| Early exit for sequential work | 3,700 tokens (92%) | Small/sequential tasks |
| Agent work package templates | 10,000 tokens (80%) | All agent setups |
| Progressive agent activation | 11,000 tokens (70%) | Right-sizing agent count |
| Dependency graph caching | 800 tokens (89%) | Resumed sessions |
| Bash-based project analysis | 1,200 tokens (60%) | Always |
| Inter-agent communication | 600 tokens (60%) | Each sync point |

**Key Insight:** Multi-agent orchestration is token-intensive by nature, but through state caching, template-based packages, and progressive scaling, we achieve 65-70% token reduction while maintaining full coordination capabilities. Early exit patterns provide 92% savings when parallelization isn't beneficial.

## Session Intelligence

I'll maintain multi-agent coordination state across sessions:

**Session Files (in current project directory):**
- `parallel-agents/orchestration.md` - Master task breakdown and agent assignments
- `parallel-agents/state.json` - Coordination state and progress
- `parallel-agents/agents/` - Individual agent work directories
- `parallel-agents/sync/` - Inter-agent communication and merge points

**IMPORTANT:** Session files are stored in a `parallel-agents` folder in your current project root

**Auto-Detection:**
- If session exists: Resume coordination with agent sync
- If no session: Analyze task and plan parallelization
- Commands: `resume`, `status`, `sync`, `merge`, `new`

## Phase 1: Task Analysis & Decomposition

### Extended Thinking for Task Orchestration

For complex multi-agent workflows, I'll use extended thinking to design optimal distribution:

<think>
When orchestrating multiple agents:
- Task dependencies and critical paths
- Work that can truly run in parallel vs sequential requirements
- Inter-agent communication boundaries and data sharing
- Merge conflict potential and resolution strategies
- Load balancing across agents based on complexity
- Rollback strategies if any agent fails
- Optimal number of agents for diminishing returns analysis
</think>

**Triggers for Extended Analysis:**
- Large-scale refactoring across many files
- Multi-component feature implementation
- Parallel test suite execution
- Documentation generation for large codebases
- Security scanning of multiple subsystems

First, I'll analyze your task to determine parallelization potential:

**Task Complexity Analysis:**
```bash
# Analyze codebase size and structure
analyze_project() {
    total_files=$(find . -type f \( -name "*.js" -o -name "*.ts" -o -name "*.py" \) | wc -l)
    total_lines=$(find . -type f \( -name "*.js" -o -name "*.ts" -o -name "*.py" \) -exec wc -l {} + | tail -1 | awk '{print $1}')

    echo "Project size: $total_files files, $total_lines lines"

    # Identify independent modules
    if [ -d "src" ]; then
        module_count=$(find src -maxdepth 1 -type d | tail -n +2 | wc -l)
        echo "Detected $module_count potential modules for parallel work"
    fi
}
```

**Task Categorization:**

1. **Highly Parallelizable** (3-5 agents recommended):
   - Independent module refactoring
   - Multi-component feature implementation
   - Parallel test suite execution
   - Documentation for separate subsystems
   - Security scanning across services

2. **Moderately Parallelizable** (2-3 agents):
   - Related component updates
   - API implementation with tests
   - Migration with validation
   - Code review of large changes

3. **Sequential Work** (1 agent):
   - Tightly coupled refactoring
   - Single-file modifications
   - Architectural changes requiring coordination

## Phase 2: Agent Configuration

Based on analysis, I'll configure the optimal agent setup:

**Boris Cherny's 5-Instance Workflow Pattern:**

```markdown
# Standard 5-Agent Configuration

## Agent Roles:

### Agent 1: Orchestrator (This Instance)
- **Role**: Master coordinator
- **Responsibilities**:
  - Task decomposition
  - Agent coordination
  - Merge conflict resolution
  - Progress monitoring
  - Final integration

### Agent 2: Backend/Logic
- **Role**: Server-side implementation
- **Workspace**: `parallel-agents/agents/backend/`
- **Focus**: API, services, business logic
- **Git Branch**: `parallel/backend-work`

### Agent 3: Frontend/UI
- **Role**: Client-side implementation
- **Workspace**: `parallel-agents/agents/frontend/`
- **Focus**: Components, UI, interactions
- **Git Branch**: `parallel/frontend-work`

### Agent 4: Testing/QA
- **Role**: Test implementation
- **Workspace**: `parallel-agents/agents/testing/`
- **Focus**: Unit tests, integration tests, E2E
- **Git Branch**: `parallel/testing-work`

### Agent 5: Documentation/DevOps
- **Role**: Documentation and tooling
- **Workspace**: `parallel-agents/agents/docs/`
- **Focus**: README, API docs, CI/CD, scripts
- **Git Branch**: `parallel/docs-work`
```

**Dynamic Agent Scaling:**
```
Task Complexity → Agent Count

Small (< 10 files):     1-2 agents
Medium (10-50 files):   2-3 agents
Large (50-200 files):   3-5 agents
Massive (200+ files):   5+ agents (with sub-orchestrators)
```

## Phase 3: Work Distribution

I'll create detailed work packages for each agent:

**Work Package Template:**
```markdown
# Agent 2: Backend Work Package

## Assigned Tasks
1. Implement UserService refactoring
   - Files: `src/services/UserService.js`, `src/models/User.js`
   - Expected output: Modern async/await patterns
   - Dependencies: None

2. Create new API endpoints
   - Files: `src/routes/users.js`
   - Expected output: RESTful user management
   - Dependencies: UserService completion

3. Add input validation
   - Files: `src/middleware/validation.js`
   - Expected output: Schema validation middleware
   - Dependencies: API endpoints

## Context Files to Read
- `src/config/database.js` - DB connection patterns
- `src/utils/errors.js` - Error handling conventions
- `docs/api-design.md` - API design guidelines

## Success Criteria
- [ ] All UserService tests passing
- [ ] API endpoints follow REST conventions
- [ ] Input validation covers edge cases
- [ ] No breaking changes to existing code
- [ ] Type definitions updated (if TypeScript)

## Integration Points
- **Coordinate with Agent 3**: Frontend will consume new API
- **Coordinate with Agent 4**: Provide test cases to implement
- **Sync Point**: After UserService completion

## Git Strategy
- Branch: `parallel/backend-work`
- Commit pattern: Conventional commits
- Push to origin for coordination
- No merge to main until orchestrator approval

## Session Commands
\`\`\`bash
# In new Claude instance:
cd /path/to/project
git checkout -b parallel/backend-work

# Follow work package tasks
# Use /refactor, /test, /review as needed
# Commit incrementally
\`\`\`
```

## Phase 4: Inter-Agent Coordination

I'll establish coordination mechanisms between agents:

**Sync Points:**
```markdown
# Synchronization Schedule

## Sync Point 1: Foundation (30 minutes)
- **Agent 2**: UserService interface defined
- **Agent 3**: Component structure created
- **Agent 4**: Test scaffolding ready
- **Agent 5**: CI pipeline configured

**Sync Action**: Orchestrator validates interfaces match

## Sync Point 2: Implementation (1 hour)
- **Agent 2**: Core logic complete
- **Agent 3**: UI components implemented
- **Agent 4**: Unit tests written
- **Agent 5**: Documentation drafted

**Sync Action**: Integration testing begins

## Sync Point 3: Integration (1.5 hours)
- **All Agents**: Work complete
- **Orchestrator**: Merge coordination

**Sync Action**: Final merge and validation
```

**Communication Protocol:**
```markdown
# Inter-Agent Communication

## File-Based Signaling
Each agent writes status to shared location:

`parallel-agents/sync/agent-2-status.json`:
\`\`\`json
{
  "agent": "backend",
  "status": "in_progress",
  "completed_tasks": 2,
  "total_tasks": 3,
  "blocking_issues": [],
  "ready_for_integration": false,
  "last_update": "2026-01-25T18:30:00Z"
}
\`\`\`

## Blocking Issues Protocol
If Agent 2 encounters blocking issue:

1. Write to `parallel-agents/sync/blocking-issue.md`
2. Orchestrator reviews and provides guidance
3. Other agents can continue on non-blocked work

## Integration Readiness
Agent signals ready with:
\`\`\`bash
echo "READY" > parallel-agents/sync/agent-2-ready.flag
git push origin parallel/backend-work
\`\`\`
```

## Phase 5: Conflict Prevention & Resolution

I'll implement strategies to minimize and resolve conflicts:

**Conflict Prevention:**

1. **Clear File Ownership:**
   ```markdown
   # File Ownership Map

   ## Agent 2 (Backend) - Exclusive Write
   - src/services/UserService.js
   - src/models/User.js
   - src/routes/users.js

   ## Agent 3 (Frontend) - Exclusive Write
   - src/components/UserProfile.jsx
   - src/hooks/useUser.js

   ## Shared Read-Only (No modifications)
   - src/types/User.d.ts (coordinated changes only)
   - package.json (orchestrator only)
   ```

2. **Module Boundaries:**
   - Assign complete modules to single agent
   - Shared dependencies managed by orchestrator
   - Interface changes require sync point

3. **Git Branch Strategy:**
   ```bash
   main
   ├── parallel/backend-work (Agent 2)
   ├── parallel/frontend-work (Agent 3)
   ├── parallel/testing-work (Agent 4)
   └── parallel/docs-work (Agent 5)

   # Orchestrator merges to integration branch first
   └── parallel/integration
   ```

**Conflict Resolution:**
```markdown
# Merge Conflict Handling

## Orchestrator Responsibilities:

1. **Detect Conflicts:**
   \`\`\`bash
   git checkout parallel/integration
   git merge parallel/backend-work    # Success
   git merge parallel/frontend-work   # Conflict in types
   \`\`\`

2. **Analyze Conflict:**
   - Read both versions
   - Understand intent of each change
   - Determine correct resolution

3. **Resolve & Validate:**
   - Apply semantic merge
   - Run tests from both branches
   - Verify no functionality broken

4. **Communicate Resolution:**
   - Document decision in merge commit
   - Update affected agents if needed
```

## Phase 6: Progressive Integration

I'll merge agent work systematically:

**Integration Strategy:**

```markdown
# Progressive Merge Plan

## Phase 1: Foundation Components (Low Risk)
- Merge Agent 5 (docs/tooling) - no code conflicts
- Merge Agent 4 (tests) - establishes validation
- Validate: CI passes, tests run

## Phase 2: Backend Implementation (Medium Risk)
- Merge Agent 2 (backend) to integration branch
- Run Agent 4's tests against backend
- Validate: All backend tests pass

## Phase 3: Frontend Integration (Medium Risk)
- Merge Agent 3 (frontend) to integration branch
- Run E2E tests
- Validate: Frontend connects to backend correctly

## Phase 4: Final Validation (High Risk)
- Full test suite execution
- Integration tests across all components
- Performance benchmarks
- Security scan

## Phase 5: Merge to Main (Production)
- All validations green
- Documentation complete
- Team review (if required)
- Merge integration branch to main
```

**Validation at Each Step:**
```bash
#!/bin/bash
# Integration validation script

validate_merge() {
    local branch=$1

    echo "Validating merge of $branch..."

    # Merge branch
    git merge --no-ff "$branch" -m "Integrate $branch"

    # Run tests
    npm test || {
        echo "Tests failed after merging $branch"
        git merge --abort
        return 1
    }

    # Run linting
    npm run lint || {
        echo "Lint failed after merging $branch"
        git reset --hard HEAD^
        return 1
    }

    # Run build
    npm run build || {
        echo "Build failed after merging $branch"
        git reset --hard HEAD^
        return 1
    }

    echo "✓ $branch integrated successfully"
    return 0
}

# Progressive integration
validate_merge "parallel/docs-work"
validate_merge "parallel/testing-work"
validate_merge "parallel/backend-work"
validate_merge "parallel/frontend-work"
```

## Phase 7: Session State Management

**Tracking Progress:**
```json
{
  "orchestration_session": "parallel_2026_01_25_1800",
  "task": "Implement user authentication system",
  "agents": {
    "backend": {
      "status": "completed",
      "branch": "parallel/backend-work",
      "tasks_complete": 3,
      "tasks_total": 3,
      "ready_for_merge": true
    },
    "frontend": {
      "status": "in_progress",
      "branch": "parallel/frontend-work",
      "tasks_complete": 2,
      "tasks_total": 3,
      "ready_for_merge": false
    },
    "testing": {
      "status": "blocked",
      "branch": "parallel/testing-work",
      "tasks_complete": 1,
      "tasks_total": 3,
      "blocking_issue": "Waiting for backend API contracts",
      "ready_for_merge": false
    }
  },
  "sync_points_completed": 1,
  "sync_points_total": 3,
  "integration_status": "not_started",
  "conflicts_detected": 0,
  "conflicts_resolved": 0
}
```

## Context Continuity

**Session Resume:**
When you return and run `/parallel-agents` or `/parallel-agents resume`:
- Load orchestration state
- Check status of all agent branches
- Display progress dashboard
- Continue from last sync point

**Progress Example:**
```
PARALLEL AGENTS ORCHESTRATION
═══════════════════════════════════════════════════

Task: Implement user authentication system
Agents: 4 active

Agent Status:
├── Backend (Agent 2):     ✓ Complete (3/3 tasks)
├── Frontend (Agent 3):    ⏳ In Progress (2/3 tasks)
├── Testing (Agent 4):     ⚠️  Blocked (1/3 tasks)
└── Docs (Agent 5):        ✓ Complete (2/2 tasks)

Sync Points: 1/3 completed
Integration: Not started
Conflicts: 0 detected

Next Action: Wait for Agent 3 to complete, then unblock Agent 4

Commands:
  /parallel-agents status    # Check all agents
  /parallel-agents sync      # Trigger sync point
  /parallel-agents merge     # Begin integration
```

## Practical Examples

**Start Orchestration:**
```
/parallel-agents "implement auth system"              # Auto-plan
/parallel-agents "refactor services" agents:3         # 3 agents
/parallel-agents resume                               # Continue session
```

**Coordination Commands:**
```
/parallel-agents status      # Check all agent status
/parallel-agents sync        # Trigger sync point
/parallel-agents merge       # Begin integration
/parallel-agents resolve     # Resolve conflicts
/parallel-agents validate    # Run validations
```

## Multi-Instance Workflow

**How to Actually Use Multiple Instances:**

1. **Setup (in this instance):**
   ```
   /parallel-agents "implement feature X"
   ```
   This creates work packages in `parallel-agents/agents/`

2. **Open Additional Claude Instances:**
   - Instance 2: Read `parallel-agents/agents/backend/work-package.md`
   - Instance 3: Read `parallel-agents/agents/frontend/work-package.md`
   - Instance 4: Read `parallel-agents/agents/testing/work-package.md`

3. **Each Instance Works Independently:**
   ```bash
   # In Instance 2:
   git checkout -b parallel/backend-work
   # Implement assigned tasks
   # Commit and push
   echo "READY" > parallel-agents/sync/agent-2-ready.flag
   ```

4. **Orchestrator Monitors & Integrates:**
   ```
   /parallel-agents status    # Check progress
   /parallel-agents merge     # When all ready
   ```

## Safety Guarantees

**Protection Measures:**
- Git branches prevent conflicts
- Orchestrator controls all merges
- Validation at each integration step
- Rollback capability for failed merges

**Important:** I will NEVER:
- Merge without validation
- Override agent work without coordination
- Push to main without full integration
- Add AI attribution to any commits

## Skill Integration

Multi-agent workflows integrate with other skills:
- `/refactor` - Individual agents use for their modules
- `/test` - Each agent validates their work
- `/review` - Final code review before merge
- `/commit` - Standardized commits across agents
- `/session-*` - Track overall orchestration session

## Token Budget Optimization

To stay within 3,000-5,000 token budget:
- **Focus on coordination logic, not implementation details**
- **Provide work packages as files, not in responses**
- **Use JSON for state tracking**
- **Defer detailed work to individual agents**
- **Compact progress reporting**

## What I'll Actually Do

1. **Analyze task** - Extended thinking for optimal decomposition
2. **Plan parallelization** - Determine agent count and roles
3. **Create work packages** - Detailed instructions per agent
4. **Setup coordination** - Branches, sync points, communication
5. **Monitor progress** - Track agent status via files/git
6. **Integrate systematically** - Progressive merge with validation
7. **Resolve conflicts** - Intelligent merge conflict resolution
8. **Validate completely** - Full testing before main merge

I'll orchestrate multiple Claude instances to accomplish complex tasks faster through intelligent parallel execution.

---

**Credits:**
- Based on Boris Cherny's 5-instance parallel workflow
- Inspired by obra/superpowers collaboration patterns
- Git branching strategies from industry best practices
- Multi-agent systems design patterns
- Distributed development workflows from large-scale projects

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manastalukdar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
