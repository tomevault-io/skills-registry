---
name: equilateral-agents-refactored
description: Multi-agent orchestration system sử dụng Claude subagents thực tế từ thư mục agents/ cho security reviews, code quality analysis, deployment validation, infrastructure checks. Auto-activates với orchestrator-worker pattern và extended thinking mode. Use when this capability is needed.
metadata:
  author: wollfoo
---

# EquilateralAgents - Multi-Agent Orchestration (Refactored)

**Hệ thống orchestration đa agents sử dụng Claude subagents thực tế** - không cần implementation code external.

## 🎯 Kiến Trúc: Orchestrator-Worker Pattern

Dựa trên [Anthropic Multi-Agent Research System](https://www.anthropic.com/engineering/multi-agent-research-system):

```
┌──────────────────────────────────────────┐
│   Lead Agent (Orchestrator)              │
│   - Extended Thinking Mode               │
│   - Task Decomposition                   │
│   - Strategy Planning                    │
│   - Result Synthesis                     │
└────────────┬─────────────────────────────┘
             │
             ├─────> Parallel Subagent Execution
             │
    ┌────────┼────────┬────────┬────────┐
    │        │        │        │        │
    ▼        ▼        ▼        ▼        ▼
┌────────┐ ┌─────┐ ┌──────┐ ┌──────┐ ┌────┐
│Security│ │Code │ │Tester│ │DevOps│ │...│
│Auditor │ │Review│ │Agent │ │Agent │ │   │
└────────┘ └─────┘ └──────┘ └──────┘ └────┘
    │        │        │        │        │
    └────────┴────────┴────────┴────────┘
             │
             ▼
    ┌─────────────────┐
    │ Result Synthesis│
    │ & Aggregation   │
    └─────────────────┘
```

## 📋 Available Agents (53 Production-Ready Agents)

### **Tier 1: Core Production Agents (16 agents)** ⭐⭐⭐

#### **1. Security & Quality (4 agents)** 🛡️
Mission-critical agents cho code safety và production reliability:
- `security-auditor` - Comprehensive security audit, vulnerability scanning, OWASP compliance
- `code-reviewer` - Code quality, best practices, static analysis, security patterns
- `tester` - Test execution, coverage ≥80% unit / ≥70% integration, QA validation
- `performance-engineer` - Performance optimization, benchmarking, bottleneck detection

**Auto-activation**: security, vulnerability, audit, review, test, coverage, qa, performance, optimization

---

#### **2. Architecture & Planning (5 agents)** 📐
Strategic agents cho system design và research:
- `planner-researcher` - Technical research, system design, planning, best practices
- `architect-review` - Architecture review, design patterns, system evaluation
- `backend-architect` - Backend systems, API design (REST/GraphQL/gRPC), microservices
- `graphql-architect` - GraphQL schema, federation, resolver optimization, DataLoader
- `cloud-architect` - Cloud architecture, AWS/GCP/Azure, infrastructure as code

**Auto-activation**: research, plan, architecture, design, analyze, microservices, graphql, cloud

---

#### **3. Development (7 agents)** 💻
Core implementation specialists:
- `frontend-developer` - React/Vue, UI components, responsive design, modern frameworks
- `mobile-developer` - React Native, Flutter, iOS/Android, native platforms
- `database-specialist` - Database design, query optimization, migrations, indexing
- `devops-engineer` - CI/CD, infrastructure automation, container orchestration
- `data-engineer` - ETL workflows, data pipelines, analytics, data warehouse
- `code-searcher` - Codebase analysis, pattern detection, dependency mapping, navigation
- `codebase-research-analyst` - Deep codebase research, architecture analysis, impact assessment

**Auto-activation**: frontend, mobile, database, devops, deployment, ci/cd, data pipeline, search, analyze, architecture

---

### **Tier 2: Specialized Experts (12 agents)** ⭐⭐

#### **4. Language Specialists (7 agents)** 🎯
Language-specific và technology experts:
- `typescript-expert` - TypeScript, type safety, advanced patterns, generics
- `python-pro` - Python, async programming, FastAPI, Django, type hints
- `golang-pro` - Go development, concurrency, goroutines, channels
- `rust-pro` - Rust systems programming, memory safety, zero-cost abstractions
- `ruby-pro` - Ruby, SOLID principles, service objects, RSpec testing
- `blockchain-developer` - Smart contracts, Solidity, Web3, dApp development
- `hyperledger-fabric-developer` - Hyperledger Fabric, chaincode, permissioned blockchain

**Auto-activation**: typescript, python, golang, rust, ruby, blockchain, solidity, web3, hyperledger

---

#### **5. Data & AI (3 agents)** 🤖
Machine learning và data science specialists:
- `ml-engineer` - Machine learning, model deployment, training pipelines, MLOps
- `data-scientist` - Data analysis, statistical modeling, predictive analytics
- `context-manager` - Context management, RAG optimization, memory coordination

**Auto-activation**: machine learning, ml, mlops, data science, rag, context

---

#### **6. Design & UX (2 agents)** 🎨
UI/UX design specialists:
- `ui-ux-designer` - UI/UX design, accessibility (WCAG), design systems, user research
- `frontend-designer` - Frontend design implementation, component libraries

**Auto-activation**: ui, ux, design, accessibility, wcag, design system

---

### **Tier 3: Extended Coverage (25 agents)** ⭐

#### **Quality & Refactoring (3 agents)**
- `debug-specialist` - Debugging, root cause analysis, error fixing
- `code-refactor-master` - Code refactoring, technical debt reduction
- `plan-reviewer` - Plan validation, risk assessment, quality checks

#### **Planning & Coordination (3 agents)**
- `planning-strategist` - Strategic planning, requirements analysis
- `project-task-planner` - Task planning, project management
- `refactor-planner` - Refactoring planning, code quality improvements

#### **Documentation & Content (4 agents)**
- `docs-architect` - Documentation architecture, developer guides, API docs
- `technical-documentation-specialist` - Technical writing, JSDoc, code documentation
- `prd-writer` - Product requirements documents, technical specs
- `content-writer` - Content creation, copywriting

#### **Finance & Trading (6 agents)**
- `quant-analyst` - Quantitative finance, trading algorithms, risk metrics
- `crypto-analyst` - Crypto market analysis, technical indicators
- `crypto-trader` - Crypto trading strategies, automated execution
- `crypto-risk-manager` - Crypto risk management, portfolio optimization
- `defi-strategist` - DeFi strategies, protocol analysis
- `arbitrage-bot` - Arbitrage detection, automated trading bots

#### **Specialized Domains (6 agents)**
- `game-developer` - Game development, Unity, game mechanics
- `payment-integration` - Stripe, PayPal, payment processors, PCI compliance
- `php-developer` - PHP, PSR standards, Laravel, dependency injection
- `legacy-modernizer` - Legacy system modernization, migration strategies
- `web-research-specialist` - Web research, information gathering
- `vibe-coding-coach` - Vision-driven coding, creative development

#### **Utilities & Support (3 agents)**
- `memory-bank-synchronizer` - Memory management, documentation sync
- `tech-knowledge-assistant` - Knowledge sharing, education, concept explanation
- `get-current-datetime` - Date/time utilities, timezone handling

**Full agent list**: See `agents/` directory (53 total agents)

## 🚀 Workflows Sử Dụng Subagents Thực Tế

### Workflow 1: Security Review (Multi-Agent)

**Command**: `/ea:security-review`

**Lead Agent Strategy** (Extended Thinking):
```markdown
## Extended Thinking Process:
1. Analyze codebase complexity and scope
2. Determine required agents: security-auditor (primary), code-reviewer (secondary)
3. Plan parallel execution strategy
4. Define result aggregation approach
```

**Implementation**:
```javascript
// Phase 1: Lead Agent Planning (với extended thinking)
Lead Agent (You):
  - Analyze request complexity
  - Decompose vào subtasks: vuln scanning, code review, compliance check
  - Decide: spawn 2 subagents parallel

// Phase 2: Spawn Subagents
Task 1 (Parallel):
  Agent: security-auditor
  Context: {
    projectPath: "./",
    scanDepth: "comprehensive",
    focus: ["authentication", "injection", "secrets"]
  }
  
Task 2 (Parallel):
  Agent: code-reviewer
  Context: {
    focus: "security",
    checkFor: ["OWASP", "CWE", "input-validation"]
  }

// Phase 3: Result Synthesis
Lead Agent:
  - Aggregate results từ 2 agents
  - Deduplicate findings
  - Prioritize by severity
  - Generate unified security report
```

**Output Structure**:
```markdown
# Security Review Report (Multi-Agent)

## Executive Summary
[Synthesized từ cả 2 agents]

## Critical Vulnerabilities (Aggregated)
### From security-auditor:
- [Finding 1 với evidence]

### From code-reviewer:
- [Finding 2 với code analysis]

## Confidence Scores
- security-auditor: 95% (deep scan)
- code-reviewer: 90% (static analysis)
- Combined confidence: 92%

## Audit Trail
- Agent 1: security-auditor @ 2025-01-09 20:30:15
- Agent 2: code-reviewer @ 2025-01-09 20:30:18
- Synthesis: lead-agent @ 2025-01-09 20:32:45
```

---

### Workflow 2: Code Quality Gate (Pipeline)

**Command**: `/ea:code-quality`

**Pipeline Strategy**:
```
code-searcher → code-reviewer → tester → synthesis
  (analysis)    (quality check)  (validation)  (report)
```

**Implementation**:
```javascript
// Sequential pipeline với dependencies

// Step 1: Codebase Analysis
Task.spawn({
  agent: "code-searcher",
  objective: "Analyze codebase structure, patterns, complexity",
  output: "analysis-summary.json"
})

// Step 2: Code Review (depends on Step 1)
Task.spawn({
  agent: "code-reviewer",
  objective: "Review code quality, best practices",
  context: analysis_from_step1,
  output: "review-report.md"
})

// Step 3: Test Validation (depends on Step 2)
Task.spawn({
  agent: "tester",
  objective: "Run test suite, check coverage",
  context: review_findings,
  output: "test-results.json"
})

// Step 4: Synthesis
Lead Agent:
  - Quality Score: calculate from all metrics
  - Technical Debt: aggregate findings
  - Action Items: prioritize recommendations
```

---

### Workflow 3: Deploy Feature (Hierarchical)

**Command**: `/ea:deploy-feature`

**Hierarchical Strategy**:
```
            planner-researcher (coordinator)
                      |
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
  tester      security-auditor  devops-engineer
  (pre-check)   (validation)    (deployment)
```

**Implementation**:
```javascript
// Coordinator: planner-researcher
Task.spawn({
  agent: "planner-researcher",
  objective: "Create deployment plan và coordinate sub-agents",
  subtasks: [
    {
      agent: "tester",
      task: "Run full test suite + smoke tests",
      blocking: true  // Must pass trước khi deploy
    },
    {
      agent: "security-auditor", 
      task: "Security validation + CVE check",
      blocking: true
    },
    {
      agent: "devops-engineer",
      task: "Execute deployment với rollback plan",
      dependsOn: ["tester", "security-auditor"]
    }
  ]
})

// planner-researcher orchestrates và monitors
// Nếu test fail → abort deployment
// Nếu security issues → fix then retry
// Nếu deployment fail → auto rollback
```

---

### Workflow 4: Infrastructure Check (Parallel + Synthesis)

**Command**: `/ea:infrastructure-check`

**Strategy**: Parallel analysis với specialized agents

```javascript
// Parallel execution cho fast results
const tasks = await Promise.all([
  Task.spawn({
    agent: "devops-engineer",
    focus: "IaC validation, Terraform/K8s configs"
  }),
  Task.spawn({
    agent: "security-auditor",
    focus: "Infrastructure security, IAM policies"
  }),
  Task.spawn({
    agent: "database-specialist",
    focus: "Database configs, backup strategies"
  }),
  Task.spawn({
    agent: "performance-engineer",
    focus: "Resource sizing, cost optimization"
  })
])

// Lead agent synthesizes
Lead Agent:
  - Aggregate all findings
  - Cross-reference issues (e.g., security + cost)
  - Generate unified infrastructure report
  - Recommend optimization priorities
```

---

## 🎮 Usage Instructions

### Basic Usage (Auto-Orchestration)

```bash
# Lead agent tự động orchestrate
/ea:security-review

# Workflow tự động:
# 1. Lead agent phân tích request
# 2. Spawn appropriate subagents
# 3. Parallel execution
# 4. Result synthesis
```

### Advanced Usage (Manual Control)

```bash
# Specify agents manually
/ea:custom --agents=security-auditor,code-reviewer,tester \
           --mode=parallel \
           --output=comprehensive

# Pipeline mode
/ea:custom --agents=code-searcher→code-reviewer→tester \
           --mode=pipeline
```

### Context-Based Auto-Activation

| User Intent | Triggered Workflow | Agents Used |
|-------------|-------------------|-------------|
| "audit security" | security-review | security-auditor, code-reviewer |
| "review code quality" | code-quality | code-searcher, code-reviewer, tester |
| "deploy to production" | deploy-feature | planner-researcher, tester, security-auditor, devops-engineer |
| "optimize performance" | performance-check | performance-engineer, database-specialist, code-reviewer |
| "check infrastructure" | infrastructure-check | devops-engineer, security-auditor, database-specialist |

---

## 🧠 Extended Thinking Mode (Lead Agent)

Lead agent sử dụng **Extended Thinking** để plan strategy:

```markdown
<thinking>
## Task Analysis
- Complexity: HIGH (multi-domain security review)
- Required expertise: Security + Code Quality
- Optimal strategy: Parallel execution với 2 specialized agents

## Agent Selection Reasoning
- security-auditor: Deep vulnerability scanning, OWASP compliance
- code-reviewer: Static analysis, code quality metrics
- Rationale: Complementary capabilities, no overlap

## Execution Plan
1. Spawn both agents parallel (independent subtasks)
2. security-auditor: Focus on auth, injection, secrets
3. code-reviewer: Focus on input validation, error handling
4. Wait for both completions
5. Synthesize results, deduplicate findings
6. Generate unified report với combined confidence scores

## Risk Assessment
- Risk: Potential duplicate findings → Mitigation: Deduplication logic
- Risk: Conflicting recommendations → Mitigation: Prioritize by severity
</thinking>

<execution>
[Spawn subagents và execute...]
</execution>

<synthesis>
[Aggregate results và generate final report]
</synthesis>
```

---

## 📊 Evidence-Based Reporting

**Luôn cung cấp evidence cụ thể**:

### ✅ Good Example:
```markdown
✅ Security Review Complete (Multi-Agent)

**Findings** (Aggregated):
- Critical: 2 issues
  - SQL Injection risk @ auth/login.ts:45 (security-auditor, confidence: 95%)
  - Hardcoded API key @ config/env.ts:12 (code-reviewer, confidence: 100%)
  
- High: 5 issues
- Medium: 12 issues

**Coverage**:
- Files scanned: 342
- security-auditor: 100% coverage (15min)
- code-reviewer: 100% coverage (12min)
- Total execution time: 15min (parallel)

**Audit Trail**:
- Workflow: security-review-20250109-203015
- Agents: security-auditor, code-reviewer
- Results: ./reports/security-review-20250109.md
```

### ❌ Avoid:
```markdown
❌ Security check complete
❌ Found some issues
❌ Review done
```

---

## 🔄 Agent Coordination Patterns

### Pattern 1: Fan-Out / Fan-In (Parallel)
```
Lead → [Agent1, Agent2, Agent3] → Synthesis
```
**Use case**: Independent tasks, fast results needed

### Pattern 2: Pipeline (Sequential)
```
Agent1 → Agent2 → Agent3 → Final
```
**Use case**: Dependencies, output of one feeds next

### Pattern 3: Hierarchical (Tree)
```
        Coordinator
         /    |    \
    Agent1  Agent2  Agent3
     /  \     |      / \
   A1.1 A1.2 A2.1  A3.1 A3.2
```
**Use case**: Complex workflows, sub-orchestration

### Pattern 4: Mesh (Collaborative)
```
Agent1 ←→ Agent2
  ↕         ↕
Agent3 ←→ Agent4
```
**Use case**: Cross-agent context sharing (dùng context-manager)

---

## 🛠️ Context Management

**Sử dụng `context-manager` agent** cho cross-agent communication:

```javascript
// Store context cho agents khác
Task.spawn({
  agent: "context-manager",
  action: "store",
  data: {
    key: "security-findings",
    value: findings,
    sharedWith: ["code-reviewer", "tester"]
  }
})

// Retrieve context
Task.spawn({
  agent: "code-reviewer",
  beforeExecution: {
    retrieveContext: "security-findings"
  }
})
```

---

## 🎓 Best Practices

### 1. **Agent Selection**
- ✅ Choose specialists for their domain
- ✅ Avoid overlapping capabilities
- ✅ Consider agent execution time

### 2. **Orchestration**
- ✅ Use parallel for independent tasks
- ✅ Use pipeline cho dependencies
- ✅ Monitor agent status

### 3. **Context Sharing**
- ✅ Minimize context transfer (giảm token usage)
- ✅ Use structured data format
- ✅ Cache frequently used context

### 4. **Error Handling**
- ✅ Implement fallback agents
- ✅ Set timeouts cho mỗi agent
- ✅ Graceful degradation

### 5. **Result Synthesis**
- ✅ Deduplicate findings
- ✅ Prioritize by severity
- ✅ Provide confidence scores
- ✅ Include audit trails

---

## 🔍 Debugging & Monitoring

### Check Agent Status
```bash
# List active agents
/ea:status

# Output:
# Active Agents:
# - security-auditor: RUNNING (8min 32s)
# - code-reviewer: COMPLETED (12min)
# - tester: QUEUED
```

### View Execution History
```bash
# Show workflow history
/ea:history --limit=10

# Output shows:
# - Workflow ID
# - Agents used
# - Execution time
# - Results location
```

### Agent Performance Metrics
```markdown
## Agent Performance Report

| Agent | Avg Time | Success Rate | Token Usage |
|-------|----------|--------------|-------------|
| security-auditor | 15min | 98% | ~45K tokens |
| code-reviewer | 12min | 99% | ~38K tokens |
| tester | 8min | 95% | ~25K tokens |
```

---

## 📦 File Structure

```
.equilateral/
├── workflows/
│   ├── security-review-20250109-203015.json
│   └── code-quality-20250109-210430.json
├── results/
│   ├── security-review-20250109.md
│   └── code-quality-20250109.md
└── audit/
    ├── agent-logs/
    │   ├── security-auditor-20250109.log
    │   └── code-reviewer-20250109.log
    └── performance-metrics.json

reports/
├── security-review-20250109.md
└── quality-gate-20250109.md
```

---

## 🚀 Migration từ Old Version

### Old (External Code):
```javascript
// Yêu cầu external implementation
const AgentOrchestrator = require('./equilateral-core/AgentOrchestrator');
orchestrator.registerAgent(new SecurityScannerAgent());
```

### New (Subagents):
```javascript
// Sử dụng agents có sẵn
Task.spawn({
  agent: "security-auditor",
  objective: "Comprehensive security scan"
})
```

**Advantages**:
- ✅ Không cần external code
- ✅ Agents đã có sẵn và tested
- ✅ Native Claude integration
- ✅ Extended thinking support
- ✅ Better context management

---

## 📚 References

- [Anthropic Multi-Agent System](https://www.anthropic.com/engineering/multi-agent-research-system)
- [Claude Subagents Guide](https://www.cursor-ide.com/blog/claude-subagents)
- [Orchestrator-Worker Pattern](https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking)
- Agents Directory: `C:\Users\VIET TIEN\Desktop\claude-setup\agents\`
- Sub-agents Command: `C:\Users\VIET TIEN\Desktop\claude-setup\commands\sub-agents.md`

---

## ✨ Key Improvements vs Old Version

| Feature | Old Version | New (Refactored) |
|---------|-------------|------------------|
| **Implementation** | External code required | Native subagents |
| **Agent Count** | 22 (definition only) | 22+ (thực tế) |
| **Orchestration** | Manual registration | Auto orchestration |
| **Context** | Database-driven | Claude native context |
| **Thinking** | Sequential | Extended thinking |
| **Execution** | Blocking | Parallel + async |
| **Evidence** | Logs in JSON | Detailed reports |
| **Debugging** | Manual | Built-in monitoring |

---

## 🎯 Summary

**EquilateralAgents Refactored** = Production-ready multi-agent orchestration sử dụng **Claude subagents thực tế**, không cần external implementation, với **orchestrator-worker pattern** và **extended thinking mode** cho optimal results.

**Use case chính**: Security reviews, code quality gates, deployment validation, infrastructure checks - tất cả đều thực thi bằng agents có sẵn với auto-orchestration intelligent.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wollfoo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
