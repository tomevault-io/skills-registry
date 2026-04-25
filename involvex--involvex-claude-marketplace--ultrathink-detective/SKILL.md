---
name: ultrathink-detective
description: ⚡ PRIMARY TOOL for: 'comprehensive audit', 'deep analysis', 'full codebase review', 'multi-perspective investigation', 'complex questions'. Combines ALL detective perspectives (architect+developer+tester+debugger). Uses Opus model. REPLACES grep/glob entirely. Uses claudemem v0.3.0 AST with ALL commands (map, symbol, callers, callees, context). GREP/FIND/GLOB ARE FORBIDDEN. Use when this capability is needed.
metadata:
  author: involvex
---

# ⛔⛔⛔ CRITICAL: AST STRUCTURAL ANALYSIS ONLY ⛔⛔⛔

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                                                                              ║
║   🧠 THIS SKILL USES claudemem v0.3.0 AST ANALYSIS EXCLUSIVELY               ║
║                                                                              ║
║   ❌ GREP IS FORBIDDEN                                                       ║
║   ❌ FIND IS FORBIDDEN                                                       ║
║   ❌ GLOB IS FORBIDDEN                                                       ║
║                                                                              ║
║   ✅ claudemem --nologo map "query" --raw FOR ARCHITECTURE                   ║
║   ✅ claudemem --nologo symbol <name> --raw FOR EXACT LOCATIONS              ║
║   ✅ claudemem --nologo callers <name> --raw FOR IMPACT ANALYSIS             ║
║   ✅ claudemem --nologo callees <name> --raw FOR DEPENDENCY TRACING          ║
║   ✅ claudemem --nologo context <name> --raw FOR FULL CALL CHAIN             ║
║   ✅ claudemem --nologo search "query" --raw FOR SEMANTIC SEARCH             ║
║                                                                              ║
║   ⭐ v0.3.0: ALL commands used for comprehensive multi-dimensional analysis ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

# Ultrathink Detective Skill

**Version:** 3.3.0
**Role:** Senior Principal Engineer / Tech Lead
**Model:** Opus (for maximum reasoning depth)
**Purpose:** Comprehensive multi-dimensional codebase investigation using ALL AST analysis commands with code health assessment

## Role Context

You are investigating as a **Senior Principal Engineer**. Your analysis is:
- **Holistic** - All perspectives (architecture, implementation, testing, debugging)
- **Deep** - Beyond surface-level using full call chain context
- **Strategic** - Long-term implications from PageRank centrality
- **Evidence-based** - Every conclusion backed by AST relationships
- **Actionable** - Clear recommendations with priorities

## Why Ultrathink Uses ALL Commands

| Command | Primary Use | Ultrathink Application |
|---------|-------------|------------------------|
| `map` | Architecture overview | Dimension 1: Structure discovery |
| `symbol` | Exact locations | Pinpoint critical code |
| `callers` | Impact analysis | Dimensions 2-3: Usage patterns, test coverage |
| `callees` | Dependencies | Dimensions 4-5: Data flow, reliability |
| `context` | Full chain | Bug investigation, root cause analysis |
| `search` | Semantic query | Dimension 6: Broad pattern discovery |

## When to Use Ultrathink

- Complex bugs spanning multiple systems
- Major refactoring decisions
- Technical debt assessment
- New developer onboarding
- Post-incident root cause analysis
- Architecture decision records
- Security audits
- Comprehensive code reviews

---

## PHASE 0: MANDATORY SETUP (CANNOT BE SKIPPED)

### Step 1: Verify claudemem v0.3.0

```bash
which claudemem && claudemem --version
# Must be 0.3.0+
```

### Step 2: If Not Installed → STOP

**DO NOT FALL BACK TO GREP.** Use AskUserQuestion:

```typescript
AskUserQuestion({
  questions: [{
    question: "claudemem v0.3.0 (AST structural analysis) is required. Grep/find are NOT acceptable alternatives. How proceed?",
    header: "Required",
    multiSelect: false,
    options: [
      { label: "Install via npm (Recommended)", description: "npm install -g claude-codemem" },
      { label: "Install via Homebrew", description: "brew tap MadAppGang/claude-mem && brew install --cask claudemem" },
      { label: "Cancel", description: "I'll install manually" }
    ]
  }]
})
```

### Step 3: Check Index Status

```bash
# Check claudemem installation and index
claudemem --version && ls -la .claudemem/index.db 2>/dev/null
```

### Step 3.5: Check Index Freshness

Before proceeding with investigation, verify the index is current:

```bash
# First check if index exists
if [ ! -d ".claudemem" ] || [ ! -f ".claudemem/index.db" ]; then
  # Use AskUserQuestion to prompt for index creation
  # Options: [1] Create index now (Recommended), [2] Cancel investigation
  exit 1
fi

# Count files modified since last index
STALE_COUNT=$(find . -type f \( -name "*.ts" -o -name "*.tsx" -o -name "*.js" -o -name "*.jsx" -o -name "*.py" -o -name "*.go" -o -name "*.rs" \) \
  -newer .claudemem/index.db 2>/dev/null | grep -v "node_modules" | grep -v ".git" | grep -v "dist" | grep -v "build" | wc -l)
STALE_COUNT=$((STALE_COUNT + 0))  # Normalize to integer

if [ "$STALE_COUNT" -gt 0 ]; then
  # Get index time with explicit platform detection
  if [[ "$OSTYPE" == "darwin"* ]]; then
    INDEX_TIME=$(stat -f "%Sm" -t "%Y-%m-%d %H:%M" .claudemem/index.db 2>/dev/null)
  else
    INDEX_TIME=$(stat -c "%y" .claudemem/index.db 2>/dev/null | cut -d'.' -f1)
  fi
  INDEX_TIME=${INDEX_TIME:-"unknown time"}

  # Get sample of stale files
  STALE_SAMPLE=$(find . -type f \( -name "*.ts" -o -name "*.tsx" \) \
    -newer .claudemem/index.db 2>/dev/null | grep -v "node_modules" | grep -v ".git" | head -5)

  # Use AskUserQuestion to ask user how to proceed
  # Options: [1] Reindex now (Recommended), [2] Proceed with stale index, [3] Cancel
fi
```

**AskUserQuestion Template for Stale Index:**

```typescript
AskUserQuestion({
  questions: [{
    question: `${STALE_COUNT} files have been modified since the last index (${INDEX_TIME}). The claudemem index may be outdated, which could cause missing or incorrect results. How would you like to proceed?`,
    header: "Index Freshness Warning",
    multiSelect: false,
    options: [
      {
        label: "Reindex now (Recommended)",
        description: `Run claudemem index to update. Takes ~1-2 minutes. Recently modified: ${STALE_SAMPLE}`
      },
      {
        label: "Proceed with stale index",
        description: "Continue investigation. May miss recent code changes."
      },
      {
        label: "Cancel investigation",
        description: "I'll handle this manually."
      }
    ]
  }]
})
```

**If user selects "Proceed with stale index"**, display warning banner in output:

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  WARNING: Index is stale (${STALE_COUNT} files modified since ${INDEX_TIME})  ║
║  Results may not reflect recent code changes.                                ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

### Step 4: Index if Needed

```bash
claudemem index
```

---

## Multi-Dimensional Analysis Framework (v0.3.0)

### Dimension 1: Architecture (map command)

```bash
# Get overall structure with PageRank
claudemem --nologo map --raw

# Focus on high-PageRank symbols (> 0.05) - these ARE the architecture

# Layer identification
claudemem --nologo map "controller handler endpoint" --raw   # Presentation
claudemem --nologo map "service business logic" --raw        # Business
claudemem --nologo map "repository database query" --raw     # Data

# Pattern detection
claudemem --nologo map "factory create builder" --raw
claudemem --nologo map "interface abstract contract" --raw
claudemem --nologo map "event emit subscribe" --raw
```

### Dimension 2: Implementation (callers/callees)

```bash
# For high-PageRank symbols, trace dependencies
claudemem --nologo callees PaymentService --raw

# What calls critical code?
claudemem --nologo callers processPayment --raw

# Full dependency chain
claudemem --nologo context OrderController --raw
```

### Dimension 3: Test Coverage (callers analysis)

```bash
# Find tests for critical functions
claudemem --nologo callers authenticateUser --raw
# Look for callers from *.test.ts or *.spec.ts

# Map test infrastructure
claudemem --nologo map "test spec describe it" --raw
claudemem --nologo map "mock stub spy helper" --raw

# Coverage gaps = functions with 0 test callers
claudemem --nologo callers criticalFunction --raw
# If no test file callers → coverage gap
```

### Dimension 4: Reliability (context command)

```bash
# Error handling chains
claudemem --nologo context handleError --raw

# Exception flow
claudemem --nologo map "throw error exception" --raw
claudemem --nologo callers CustomError --raw

# Recovery patterns
claudemem --nologo map "retry fallback circuit" --raw
```

### Dimension 5: Security (symbol + callers)

```bash
# Authentication
claudemem --nologo symbol authenticate --raw
claudemem --nologo callees authenticate --raw
claudemem --nologo callers authenticate --raw

# Authorization
claudemem --nologo map "permission role check guard" --raw

# Sensitive data
claudemem --nologo map "password hash token secret" --raw
claudemem --nologo callers encrypt --raw
```

### Dimension 6: Performance (semantic search)

```bash
# Database patterns
claudemem --nologo search "query database batch" --raw

# Async patterns
claudemem --nologo map "async await promise parallel" --raw

# Caching
claudemem --nologo map "cache memoize store" --raw
```

### Dimension 7: Code Health (v0.4.0+ Required)

```bash
# Dead code detection
DEAD=$(claudemem --nologo dead-code --raw)

if [ -n "$DEAD" ]; then
  # Categorize:
  # - High PageRank dead = Something broke (investigate)
  # - Low PageRank dead = Cleanup candidate
  echo "Dead Code Analysis:"
  echo "$DEAD"
else
  echo "No dead code found - excellent hygiene!"
fi

# Test coverage gaps
GAPS=$(claudemem --nologo test-gaps --raw)

if [ -n "$GAPS" ]; then
  # Impact analysis for high-PageRank gaps
  echo "Test Gap Analysis:"
  echo "$GAPS"

  # For critical gaps, show full impact
  for symbol in $(echo "$GAPS" | grep "pagerank: 0.0[5-9]" | awk '{print $4}'); do
    echo "Impact for critical untested: $symbol"
    claudemem --nologo impact "$symbol" --raw
  done
else
  echo "No test gaps found - excellent coverage!"
fi
```

---

## Comprehensive Analysis Workflow (v0.3.0)

### Phase 1: Architecture Mapping (10 min)

```bash
# Get structural overview with PageRank
claudemem --nologo map --raw

# Document high-PageRank symbols (> 0.05)
# These are architectural pillars - understand first

# Map each layer
claudemem --nologo map "controller route endpoint" --raw
claudemem --nologo map "service business domain" --raw
claudemem --nologo map "repository data persist" --raw
```

### Phase 2: Critical Path Analysis (15 min)

```bash
# For each high-PageRank symbol:

# 1. Get exact location
claudemem --nologo symbol PaymentService --raw

# 2. Trace dependencies (what it needs)
claudemem --nologo callees PaymentService --raw

# 3. Trace usage (what depends on it)
claudemem --nologo callers PaymentService --raw

# 4. Full context for complex ones
claudemem --nologo context PaymentService --raw
```

### Phase 3: Test Coverage Assessment (10 min)

```bash
# For each critical function, check callers
claudemem --nologo callers processPayment --raw
claudemem --nologo callers authenticateUser --raw
claudemem --nologo callers updateProfile --raw

# Count:
# - Test callers (from *.test.ts, *.spec.ts)
# - Production callers

# High PageRank + 0 test callers = CRITICAL GAP
```

### Phase 4: Risk Identification (10 min)

```bash
# Security symbols
claudemem --nologo map "auth session token" --raw
claudemem --nologo callers validateToken --raw

# Error handling
claudemem --nologo map "error exception throw" --raw
claudemem --nologo context handleFailure --raw

# External integrations
claudemem --nologo map "API external webhook" --raw
claudemem --nologo callers stripeClient --raw
```

### Phase 5: Technical Debt Inventory (10 min)

```bash
# Deprecated patterns
claudemem --nologo search "TODO FIXME deprecated" --raw

# Complexity indicators (high PageRank but many callees)
claudemem --nologo callees LargeService --raw
# > 20 callees = potential god class

# Orphaned code (low PageRank, 0 callers)
claudemem --nologo callers unusedFunction --raw
```

---

## Output Format: Comprehensive Report (v0.3.0)

### Executive Summary

```
┌─────────────────────────────────────────────────────────────────┐
│           CODEBASE COMPREHENSIVE ANALYSIS (v0.3.0)               │
├─────────────────────────────────────────────────────────────────┤
│  Overall Health: 🟡 MODERATE (7.2/10)                           │
│  Search Method: claudemem v0.3.0 (AST + PageRank)               │
│                                                                  │
│  Dimensions:                                                     │
│  ├── Architecture:    🟢 GOOD      (8/10) [map analysis]        │
│  ├── Implementation:  🟡 MODERATE  (7/10) [callers/callees]     │
│  ├── Testing:         🔴 POOR      (5/10) [test-gaps]           │
│  ├── Reliability:     🟢 GOOD      (8/10) [context tracing]     │
│  ├── Security:        🟡 MODERATE  (7/10) [auth callers]        │
│  ├── Performance:     🟢 GOOD      (8/10) [async patterns]      │
│  └── Code Health:     🟡 MODERATE  (6/10) [dead-code + impact]  │
│                                                                  │
│  Critical: 3 | Major: 7 | Minor: 15                             │
└─────────────────────────────────────────────────────────────────┘
```

### Dimension 1: Architecture (from map)

```
Core Abstractions (PageRank > 0.05):
├── UserService (0.092) - Central business logic
├── Database (0.078) - Data access foundation
├── AuthMiddleware (0.056) - Security boundary
└── EventBus (0.051) - Cross-cutting concerns

Layer Structure:
┌─────────────────────────────────────────────────────────┐
│  PRESENTATION (src/controllers/)                        │
│    └── UserController (0.034)                          │
│    └── AuthController (0.028)                          │
│            ↓                                            │
│  BUSINESS (src/services/)                              │
│    └── UserService (0.092) ⭐HIGH PAGERANK             │
│    └── AuthService (0.067)                             │
│            ↓                                            │
│  DATA (src/repositories/)                              │
│    └── UserRepository (0.045)                          │
│    └── Database (0.078) ⭐HIGH PAGERANK                │
└─────────────────────────────────────────────────────────┘
```

### Dimension 2: Implementation (from callers/callees)

```
Critical Data Flows:

processPayment (PageRank: 0.045)
├── CALLEES (dependencies):
│   ├── validateCard → stripeClient.validateCard
│   ├── getCustomer → Database.query
│   ├── chargeStripe → stripeClient.charge
│   └── saveTransaction → TransactionRepository.save
│
└── CALLERS (usage):
    ├── CheckoutController.submit:45
    ├── SubscriptionService.renew:89
    └── RetryQueue.processPayment:23
```

### Dimension 3: Test Coverage (from callers)

```
| Function            | Test Callers | Prod Callers | Coverage |
|---------------------|--------------|--------------|----------|
| authenticateUser    | 5            | 12           | ✅ Good   |
| processPayment      | 3            | 8            | ✅ Good   |
| calculateDiscount   | 0            | 4            | ❌ None   |
| sendEmail           | 1            | 6            | ⚠️ Low    |
| updateUserProfile   | 0            | 3            | ❌ None   |

🔴 CRITICAL GAPS (high PageRank + 0 test callers):
   └── calculateDiscount (PageRank: 0.034)
       └── callers: 4 production, 0 tests
```

### Dimension 4: Reliability (from context)

```
Error Handling Chain:

handleAuthError (context analysis):
├── Defined: src/middleware/auth.ts:45
├── CALLERS (error sources):
│   ├── validateToken:23 → throws on invalid
│   ├── refreshSession:67 → throws on expired
│   └── checkPermission:89 → throws on denied
└── CALLEES (error handling):
    ├── logError → Logger.error
    ├── notifyAdmin → AlertService.send (if critical)
    └── formatResponse → ErrorFormatter.toJSON
```

### Dimension 5: Security (from symbol + callers)

```
Authentication Flow:

authenticate (PageRank: 0.067)
├── Location: src/services/auth.ts:23-67
├── CALLEES:
│   ├── bcrypt.compare (password verification)
│   ├── jwt.sign (token generation)
│   └── SessionStore.create (session persistence)
└── CALLERS (entry points):
    ├── LoginController.login:12 ✅
    ├── OAuthController.callback:45 ✅
    └── APIMiddleware.verify:23 ⚠️ (rate limiting?)
```

### Dimension 6: Performance (from map + callees)

```
Database Access Patterns:

UserRepository.findWithRelations (PageRank: 0.028)
├── CALLEES:
│   ├── Database.query (1 call)
│   ├── RelationLoader.load (per relation) ⚠️ N+1?
│   └── Cache.get (optimization)
└── CALLERS: 8 locations
    └── 3 in loops ⚠️ Potential N+1

Recommendation: Batch relation loading or use joins
```

---

## Action Items (Prioritized by PageRank Impact)

```
🔴 IMMEDIATE (This Sprint) - Affects High-PageRank Code

   1. Add tests for calculateDiscount (PageRank: 0.034)
      └── callers show: 4 production uses, 0 tests

   2. Fix N+1 query in UserRepository.findWithRelations
      └── callees show: RelationLoader called per item

   3. Add rate limiting to APIMiddleware.verify
      └── callers show: All API endpoints exposed

🟠 SHORT-TERM (Next 2 Sprints)

   4. Add error recovery to PaymentService
      └── context shows: No retry on Stripe failures

   5. Increase test coverage for AuthService
      └── callers show: Only 2 test files cover critical code

🟡 MEDIUM-TERM (This Quarter)

   6. Refactor UserService (PageRank: 0.092)
      └── callees show: 23 dependencies (god class pattern)

   7. Add observability to EventBus
      └── callers show: 15 publishers, no monitoring
```

---

## Result Validation Pattern

After EVERY claudemem command, validate results to ensure quality:

### Validation Per Dimension

Each dimension MUST validate its claudemem results before proceeding:

**Dimension 1: Architecture (map)**

```bash
RESULTS=$(claudemem --nologo map --raw)
EXIT_CODE=$?

# Check for command failure
if [ "$EXIT_CODE" -ne 0 ]; then
  echo "ERROR: claudemem map failed"
  # Diagnose and ask user (see Fallback Protocol below)
  exit 1
fi

# Check for empty results
if [ -z "$RESULTS" ]; then
  echo "WARNING: No architectural symbols found - index may be empty"
  # Ask user to reindex or cancel
fi

# Validate PageRank values present
if ! echo "$RESULTS" | grep -q "pagerank:"; then
  echo "WARNING: No PageRank data - index may be corrupted or outdated"
  # Ask user to reindex
fi
```

**Dimension 2-6: All Other Commands**

```bash
RESULTS=$(claudemem --nologo [command] [args] --raw)
EXIT_CODE=$?

# Check exit code
if [ "$EXIT_CODE" -ne 0 ]; then
  # Diagnose index health
  DIAGNOSIS=$(claudemem --version && ls -la .claudemem/index.db 2>&1)
  # Use AskUserQuestion for recovery options
fi

# Check for empty/irrelevant results
# Extract keywords from the user's investigation query
# Example: QUERY="how does auth work" → KEYWORDS="auth work authentication"
# The orchestrating agent must populate KEYWORDS before this check
MATCH_COUNT=0
for kw in $KEYWORDS; do
  if echo "$RESULTS" | grep -qi "$kw"; then
    MATCH_COUNT=$((MATCH_COUNT + 1))
  fi
done

if [ "$MATCH_COUNT" -eq 0 ]; then
  # Results don't match query - potentially irrelevant
  # Use AskUserQuestion (see Fallback Protocol)
fi
```

**Dimension 3: Test Coverage (callers)**

```bash
RESULTS=$(claudemem --nologo callers $FUNCTION --raw)

# Even 0 callers is valid - but validate it's not an error
if echo "$RESULTS" | grep -qi "error\|not found"; then
  # Actual error vs no callers
  # Use AskUserQuestion
fi
```

---

## FALLBACK PROTOCOL

**CRITICAL: Never use grep/find/Glob without explicit user approval.**

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                                                                              ║
║   FALLBACK PROTOCOL (NEVER SILENT)                                          ║
║                                                                              ║
║   If claudemem fails OR returns irrelevant results:                          ║
║                                                                              ║
║   1. STOP - Do not silently switch to grep/find                              ║
║   2. DIAGNOSE - Run claudemem status to check index health                   ║
║   3. COMMUNICATE - Tell user what happened                                   ║
║   4. ASK - Get explicit user permission via AskUserQuestion                  ║
║                                                                              ║
║   grep/find/Glob ARE FORBIDDEN without explicit user approval                ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

### Fallback Decision Tree

If claudemem fails or returns unexpected results:

1. **STOP** - Do not silently switch tools
2. **DIAGNOSE** - Run `claudemem status`
3. **REPORT** - Tell user what happened
4. **ASK** - Use AskUserQuestion for next steps

```typescript
// Fallback AskUserQuestion Template
AskUserQuestion({
  questions: [{
    question: "claudemem [command] failed or returned irrelevant results. How should I proceed?",
    header: "Investigation Issue",
    multiSelect: false,
    options: [
      { label: "Reindex codebase", description: "Run claudemem index (~1-2 min)" },
      { label: "Try different query", description: "Rephrase the search" },
      { label: "Use grep (not recommended)", description: "Traditional search - loses semantic understanding" },
      { label: "Cancel", description: "Stop investigation" }
    ]
  }]
})
```

### Grep Fallback Warning

If user explicitly chooses grep fallback, display this warning:

```markdown
## WARNING: Using Fallback Search (grep)

You have chosen to use grep as a fallback. Please understand the limitations:

| Feature | claudemem | grep |
|---------|-----------|------|
| Semantic understanding | Yes | No |
| Call graph analysis | Yes | No |
| Symbol relationships | Yes | No |
| PageRank ranking | Yes | No |
| False positives | Low | High |

**Recommendation:** After completing this task, run `claudemem index` to rebuild
the index for future investigations.

Proceeding with grep...
```

---

## 🚫 FORBIDDEN: DO NOT USE

```bash
# ❌ ALL OF THESE ARE FORBIDDEN
grep -r "pattern" .
rg "pattern"
find . -name "*.ts"
git grep "term"
Glob({ pattern: "**/*.ts" })
Grep({ pattern: "function" })
```

## ✅ REQUIRED: ALWAYS USE

```bash
# ✅ claudemem v0.3.0 AST Commands
claudemem --nologo map "query" --raw      # Architecture
claudemem --nologo symbol <name> --raw    # Location
claudemem --nologo callers <name> --raw   # Impact
claudemem --nologo callees <name> --raw   # Dependencies
claudemem --nologo context <name> --raw   # Full chain
claudemem --nologo search "query" --raw   # Semantic
```

---

## Cross-Plugin Integration

This skill should be used by ANY agent that needs deep analysis:

| Agent Type | Should Use | From Plugin |
|------------|-----------|-------------|
| `frontend-architect` | `ultrathink-detective` | frontend |
| `api-architect` | `ultrathink-detective` | bun |
| `senior-code-reviewer` | `ultrathink-detective` | frontend |
| Any architect agent | `ultrathink-detective` | any |

**Agents reference this skill in their frontmatter:**
```yaml
---
skills: code-analysis:ultrathink-detective
---
```

---

## ⚠️ FINAL REMINDER

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                                                                              ║
║   ULTRATHINK = ALL claudemem v0.3.0 AST COMMANDS                            ║
║                                                                              ║
║   WORKFLOW:                                                                  ║
║   1. claudemem --nologo map --raw           ← Architecture (PageRank)       ║
║   2. claudemem --nologo symbol <name> --raw ← Exact locations               ║
║   3. claudemem --nologo callers <name> --raw ← Impact analysis              ║
║   4. claudemem --nologo callees <name> --raw ← Dependencies                 ║
║   5. claudemem --nologo context <name> --raw ← Full call chain              ║
║   6. Read specific file:line (NOT whole files)                              ║
║                                                                              ║
║   ❌ grep, find, rg, Glob, Grep tool                                        ║
║                                                                              ║
║   PageRank > 0.05 = Architectural pillar = Analyze FIRST                    ║
║   High PageRank + 0 test callers = CRITICAL coverage gap                    ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

**Maintained by:** MadAppGang
**Plugin:** code-analysis v2.7.0
**Last Updated:** December 2025 (v3.3.0 - Cross-platform compatibility, inline templates, improved validation)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/involvex) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
