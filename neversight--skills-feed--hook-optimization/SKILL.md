---
name: hook-optimization
description: Provides guidance on optimizing CCPM hooks for performance and token efficiency. Auto-activates when developing, debugging, or benchmarking hooks. Includes caching strategies, token budgets, performance benchmarking, and best practices for maintaining sub-5-second hook execution times.
metadata:
  author: neversight
---

# CCPM Hook Optimization Skill

This skill provides comprehensive guidance for optimizing Claude Code hooks used in CCPM (Claude Code Project Management) to ensure high performance, minimal token usage, and reliable execution.

## Table of Contents

1. [Hook System Overview](#hook-system-overview)
2. [Performance Requirements](#performance-requirements)
3. [The Three Main Hooks](#the-three-main-hooks)
4. [Optimization Strategies](#optimization-strategies)
5. [Cached Agent Discovery](#cached-agent-discovery)
6. [Token Optimization Techniques](#token-optimization-techniques)
7. [Benchmarking & Profiling](#benchmarking--profiling)
8. [Hook Development Workflow](#hook-development-workflow)
9. [Best Practices](#best-practices)
10. [Examples & Case Studies](#examples--case-studies)

---

## Hook System Overview

### What are Claude Code Hooks?

Claude Code hooks are event-based automation points that trigger Claude to perform intelligent actions at specific moments in the development workflow:

- **UserPromptSubmit**: Triggered when user sends a message
- **PreToolUse**: Triggered before file write/edit operations
- **Stop**: Triggered after Claude completes a response

### CCPM's Three Main Hooks

| Hook | Trigger | Purpose | Target Time |
|------|---------|---------|-------------|
| **smart-agent-selector-optimized.prompt** | UserPromptSubmit | Intelligent agent selection & invocation | <5s |
| **tdd-enforcer-optimized.prompt** | PreToolUse | Ensure tests exist before code | <1s |
| **quality-gate-optimized.prompt** | Stop | Automatic code review & security audit | <5s |

### Hook Execution Flow

```
User Message
    ↓
[UserPromptSubmit Hook]
    ↓ smart-agent-selector analyzes request
    ↓ Selects best agents (with caching)
    ↓ Injects agent invocation instructions
    ↓
Claude Executes (Agents run in parallel/sequence)
    ↓
File Write/Edit Request
    ↓
[PreToolUse Hook]
    ↓ tdd-enforcer checks for tests
    ↓ Blocks if missing (invokes tdd-orchestrator)
    ↓
File Created/Modified
    ↓
Response Complete
    ↓
[Stop Hook]
    ↓ quality-gate analyzes changes
    ↓ Invokes code-reviewer, security-auditor
    ↓
Complete
```

---

## Performance Requirements

### Target Metrics

**Execution Time:**
- UserPromptSubmit hook: <5 seconds (with caching: <2 seconds)
- PreToolUse hook: <1 second
- Stop hook: <5 seconds

**Token Budget:**
- Per hook: <5,000 tokens
- Combined overhead: <10,000 tokens per user message

**Cache Performance:**
- Cache hit rate: 85-95%
- Cached execution: <100ms (vs ~2,000ms uncached)
- Cache invalidation: 5 minutes TTL

### Why These Targets Matter

```
User Experience Impact:
- <1s → Feels instant, no latency
- 1-5s → Acceptable delay
- >5s → Noticeable lag, frustrating

Token Budget Impact:
- <5,000 tokens per hook → Minimal overhead
- <10,000 tokens total → <5% of typical context window
- Well-optimized hooks → Enable more complex agent selection
```

---

## The Three Main Hooks

### 1. Smart Agent Selector (UserPromptSubmit)

**Purpose**: Analyze user request and automatically invoke best agents

**Original Version**: 19,307 lines, ~4,826 tokens
**Optimized Version**: 3,538 lines, ~884 tokens
**Improvement**: 82% token reduction

**Key Optimizations**:
- Removed verbose explanations (inline comments instead)
- Simplified response format (JSON without markdown)
- Cached agent discovery
- Conditional logic (skip for simple docs questions)

**Execution Flow**:
```
User: "Add authentication with JWT"
    ↓
[smart-agent-selector]
    ↓ Task: Implementation
    ↓ Keywords: auth, jwt, security
    ↓ Tech Stack: backend (detected)
    ↓ Score: tdd-orchestrator (85), backend-architect (95), security-auditor (90)
    ↓ Decision: Sequential execution
    ↓
Result: {
  "shouldInvokeAgents": true,
  "selectedAgents": [...],
  "execution": "sequential",
  "injectedInstructions": "..."
}
```

### 2. TDD Enforcer (PreToolUse)

**Purpose**: Ensure test files exist before writing production code

**Original Version**: 4,853 lines, ~1,213 tokens
**Optimized Version**: 2,477 lines, ~619 tokens
**Improvement**: 49% token reduction

**Key Optimizations**:
- Hardcoded test file patterns (no dynamic detection)
- Single-pass file existence check
- Simplified JSON response
- Skip expensive type inference

**Decision Matrix**:
```
Is test file?              → APPROVE (writing tests first)
Tests exist for module?    → APPROVE (tests are ready)
Config/docs file?         → APPROVE (no TDD needed)
Production code no tests? → BLOCK (invoke tdd-orchestrator)
User bypass?              → APPROVE (with warning)
```

### 3. Quality Gate (Stop Hook)

**Purpose**: Automatically invoke code review and security audit

**Original Version**: 4,482 lines, ~1,120 tokens
**Optimized Version**: 2,747 lines, ~687 tokens
**Improvement**: 39% token reduction

**Key Optimizations**:
- Reduced scoring complexity
- Fixed agent list (not dynamic)
- Simplified file type detection
- Removed verbose explanations

**Decision Rules**:
```
Code files modified?      → Invoke code-reviewer
API/auth code?            → Invoke security-auditor (blocking)
3+ files changed?         → Invoke code-reviewer
Only docs/tests?          → SKIP (no review needed)
```

---

## Optimization Strategies

### 1. Use Cached Agent Discovery

**Problem**: Full agent discovery takes ~2,000ms

**Solution**: Cache agent list with 5-minute TTL

**Implementation**:
```bash
# Original: Slow discovery
agents=$(jq -r '.plugins | keys[]' ~/.claude/plugins/installed_plugins.json)
# Result: ~2,000ms per execution

# Optimized: Cached discovery
CACHE_FILE="${TMPDIR:-/tmp}/claude-agents-cache-$(id -u).json"
CACHE_MAX_AGE=300  # 5 minutes

if [ -f "$CACHE_FILE" ]; then
  if [ $(($(date +%s) - $(stat -f %m "$CACHE_FILE"))) -lt 300 ]; then
    cat "$CACHE_FILE"  # <100ms hit
    exit 0
  fi
fi
# Result: <100ms for cache hits, 96% faster
```

**Cache Performance**:
```
First run:     2,000ms (cache miss)
Subsequent:       20ms (cache hit)
After 5 min:   2,000ms (cache expired)

Expected hit rate: 85-95% (5-minute window typical)
Expected savings: 1,900ms per cached call
```

### 2. Minimize Context Injection

**Problem**: Injecting entire codebase context bloats tokens

**Solution**: Inline only critical information

**Before** (Verbose):
```
Available agents include:
- tdd-orchestrator: This agent is responsible for writing tests following the Red-Green-Refactor workflow. It can handle unit tests, integration tests, and end-to-end tests...
- backend-architect: The backend architect provides guidance on API design, database schemas, microservices patterns...
[continues for 50 agents]
```

**After** (Concise):
```json
{
  "availableAgents": [
    {"name": "tdd-orchestrator", "score": 85, "reason": "TDD workflow"},
    {"name": "backend-architect", "score": 95, "reason": "API design"}
  ]
}
```

**Token Savings**: 60-70% reduction

### 3. Progressive Disclosure

**Concept**: Only show information when needed

**Example - Agent Selection**:
```
Level 1 (Default): Show top 3 agents with scores
Level 2 (If needed): Show all 10 agents with reasoning
Level 3 (Debugging): Show full scoring breakdown
```

**Implementation**:
```bash
# Don't include full descriptions
"availableAgents": [
  {"name": "agent-1", "score": 85}
  # Skip: "description": "Long description..."
]

# Only explain top choice
"reasoning": "Selected top 3 agents by score"
# Skip detailed reasoning for each
```

### 4. Conditional Logic (Skip Unnecessary Work)

**Problem**: Hooks run on every message, even simple ones

**Solution**: Fast-path for low-complexity requests

**Smart Agent Selector Example**:
```javascript
// Fast path: Simple docs question
if (message.includes("how to") && !message.includes("code")) {
  return {
    "shouldInvokeAgents": false,
    "reasoning": "Documentation question, skip agents"
  }
}

// Normal path: Requires agent selection
// ... full scoring algorithm
```

**TDD Enforcer Example**:
```bash
# Fast path: Test file
if [[ "$file" == *.test.* ]] || [[ "$file" == *.spec.* ]]; then
  echo '{"decision": "approve", "reason": "Test file"}'
  exit 0
fi

# Normal path: Check for test existence
# ... expensive file system operations
```

---

## Cached Agent Discovery

### How discover-agents-cached.sh Works

**Location**: `/scripts/discover-agents-cached.sh`

**Execution Flow**:
```bash
1. Check if cache file exists
   ↓ YES: Check age
   ↓ NO: Run full discovery

2. Check cache age (<5 minutes?)
   ↓ FRESH: Return cached result immediately (~100ms)
   ↓ STALE: Continue to discovery

3. Full agent discovery (expensive)
   a. Scan plugin directory
   b. Extract agent names/descriptions
   c. Scan global agents
   d. Scan project agents

4. Cache result
   Cache file: ~/.cache/claude-agents-cache-{uid}.json
   TTL: 300 seconds (5 minutes)
```

### Cache Configuration

**Cache File Location**:
```bash
CACHE_FILE="${TMPDIR:-/tmp}/claude-agents-cache-$(id -u).json"
```

**Cache Invalidation**:
- **Automatic**: 5-minute TTL
- **Manual**: Delete cache file to force refresh
```bash
rm -f "${TMPDIR:-/tmp}/claude-agents-cache-$(id -u).json"
```

**When Cache Becomes Invalid**:
- New plugin installed
- Plugin agents updated
- Project agents changed
- TTL expires (5 minutes)

### Performance Comparison

```
Scenario 1: First request after startup
  discover-agents.sh:        ~2,000ms (full scan)
  discover-agents-cached.sh: ~2,000ms (cache miss, first run)

Scenario 2: Second request (within 5 minutes)
  discover-agents.sh:        ~2,000ms (full scan again)
  discover-agents-cached.sh: ~20ms (cache hit - 100x faster!)

Typical usage (5 requests in 5 minutes):
  Without cache: 5 × 2,000ms = 10,000ms total
  With cache:    2,000ms + 20ms + 20ms + 20ms + 20ms = 2,080ms total
  Speedup:       4.8x faster
```

### Using in Hooks

**In smart-agent-selector-optimized.prompt**:
```bash
# Instead of discovering agents inline (expensive)
# Load pre-discovered agents from context
availableAgents={{availableAgents}}

# The Claude Code hook system pre-runs discovery
# and passes cached results automatically
```

---

## Token Optimization Techniques

### Technique 1: Remove Verbose Comments

**Before** (Wordy):
```
## Selection Strategy

This section describes the comprehensive strategy used to select the best agents
based on multiple factors including the user's request, the detected task type,
the technology stack in use, and various scoring algorithms...

### 1. Task Classification

The first step in the selection process is to classify the type of task the user
is requesting. This involves analyzing the user's message to determine whether
they are asking for help with...
```

**After** (Concise):
```
## Selection Strategy

### 1. Task Classification
- Planning/Design → architect agents
- Implementation → TDD first, then dev agents
- Bug Fix → debugger
```

**Token Savings**: 70% for explanatory text

### Technique 2: Use Structured Templates

**Before** (Expanded):
```
The user is asking about implementing a feature. This is an implementation task.
Based on their request mentioning "authentication" and "JWT", they're likely
working on backend authentication. The tech stack detected is Node.js/TypeScript.
With these factors, I recommend invoking tdd-orchestrator first, then
backend-architect, and finally security-auditor...
```

**After** (Templated):
```json
{
  "taskType": "implementation",
  "keywords": ["auth", "jwt"],
  "techStack": "backend",
  "selectedAgents": [
    {"name": "tdd-orchestrator", "score": 85},
    {"name": "backend-architect", "score": 95},
    {"name": "security-auditor", "score": 90}
  ]
}
```

**Token Savings**: 60% with structured format

### Technique 3: Move Examples to Comments

**Before** (Large examples):
```
### Example: Implementation Task
When the user says "Add user authentication with JWT tokens to our API",
the system should analyze this and determine that it's an implementation task
requiring TDD, architecture review, and security validation. The response would be:
{
  "shouldInvokeAgents": true,
  "selectedAgents": [
    {"name": "tdd-orchestrator", "type": "plugin", "reason": "Write tests first", "priority": "high", "score": 85},
    {"name": "backend-architect", "type": "project", "reason": "Design secure API", "priority": "high", "score": 95},
    {"name": "security-auditor", "type": "plugin", "reason": "Validate auth implementation", "priority": "high", "score": 90}
  ]
}
```

**After** (Reference examples):
```
Example: `src/hooks/examples/implementation-task.json`
```

**Token Savings**: 80% by referencing external files

### Technique 4: Eliminate Redundancy

**Before** (Repeated logic):
```
## Selection Rules
1. Use exact agent names from available agents
2. Check that agent names are valid
3. Only use agents that exist
4. Ensure agent names are correct

## Validation Rules
1. Agent must exist
2. Agent must be valid
3. Agent name must match
```

**After** (Single source):
```
## Selection Rules
1. Use exact agent names from available agents
2. Ensure all agents exist before selection
```

**Token Savings**: 50% by consolidating

---

## Benchmarking & Profiling

### Using benchmark-hooks.sh

**Location**: `/scripts/benchmark-hooks.sh`

**Run Complete Benchmark**:
```bash
./scripts/benchmark-hooks.sh
```

**Example Output**:
```
╔════════════════════════════════════════════════════════════════════════╗
║            CCPM Hook Performance Benchmark Report                      ║
╚════════════════════════════════════════════════════════════════════════╝

SECTION 1: Script Performance (Execution Time)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 discover-agents.sh (ORIGINAL)
⏱️  Average Execution Time: 2045ms
📦 Output Size: 4821 bytes
🎯 Estimated Tokens: 1205 tokens
⚠️  Performance: ACCEPTABLE (<2s)

📊 discover-agents-cached.sh (OPTIMIZED - First Run)
⏱️  Average Execution Time: 2123ms
📦 Output Size: 4892 bytes
🎯 Estimated Tokens: 1223 tokens
⚠️  Performance: ACCEPTABLE (<2s)

📊 discover-agents-cached.sh (OPTIMIZED - Cached)
⏱️  Average Execution Time: 18ms
✅ Performance: EXCELLENT (<100ms) - 96% faster with cache!

SECTION 2: Hook Prompt Files (Token Usage)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📄 smart-agent-selector.prompt (ORIGINAL)
📦 File Size: 19307 bytes
📏 Line Count: 118 lines
🎯 Estimated Tokens: 4826 tokens
⚠️  Token Usage: NEEDS OPTIMIZATION (>3000 tokens)

📄 smart-agent-selector-optimized.prompt (NEW)
📦 File Size: 3538 bytes
📏 Line Count: 79 lines
🎯 Estimated Tokens: 884 tokens
✅ Token Usage: EXCELLENT (<500 tokens)

SECTION 3: Summary & Recommendations
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 Token Usage Comparison
   Original Total: ~10071 tokens
   Optimized Total: ~3436 tokens
   Savings: ~6635 tokens (66% reduction)

🎯 Performance Targets Met:
   ✅ All hooks execute in <5 seconds
   ✅ Cached discovery runs in <100ms (96% faster)
   ✅ Token usage reduced by 60% in optimized hooks
   ✅ No functionality regression
```

### Interpreting Results

**Execution Time Metrics**:
- ✅ EXCELLENT: <100ms (responsive, no noticeable delay)
- ✅ GOOD: <500ms (acceptable)
- ⚠️ ACCEPTABLE: <2s (noticeable but tolerable)
- ⚠️ NEEDS OPTIMIZATION: <5s (getting slow)
- ❌ UNACCEPTABLE: >5s (too slow, impacts UX)

**Token Usage Metrics**:
- ✅ EXCELLENT: <500 tokens
- ✅ GOOD: <1,500 tokens
- ⚠️ ACCEPTABLE: <3,000 tokens
- ⚠️ NEEDS OPTIMIZATION: >3,000 tokens

**Cache Hit Rate**:
- ✅ EXCELLENT: >90% hit rate
- ✅ GOOD: 80-90% hit rate
- ⚠️ ACCEPTABLE: 70-80% hit rate
- ❌ PROBLEM: <70% hit rate (check TTL settings)

---

## Hook Development Workflow

### Step 1: Create Optimized Version

**File Naming Convention**:
```
Original:  hooks/my-hook.prompt
Optimized: hooks/my-hook-optimized.prompt
```

**Starting Template**:
```
You are a [brief description].

## Context
[Relevant variables from hook]

## Analysis Rules
- Rule 1
- Rule 2

## Response Format (JSON ONLY)
```json
{
  "decision": "approve|block",
  "reasoning": "..."
}
```

**Optimization Checklist**:
- [ ] Remove verbose explanations (use inline comments)
- [ ] Simplify response format (use JSON)
- [ ] Add fast paths for common cases
- [ ] Consolidate redundant sections
- [ ] Reduce example verbosity
- [ ] Move complex logic to comments
```

### Step 2: Test with Benchmark Script

```bash
# Run the benchmark
./scripts/benchmark-hooks.sh

# Focus on your hook
grep -A 20 "my-hook-optimized" output.txt

# Check metrics:
# - Execution time: <5s target
# - Token count: <5000 tokens target
# - Performance category: ✅ EXCELLENT or GOOD
```

### Step 3: Compare with Baseline

**Create Comparison Table**:
```
Metric              | Original | Optimized | Improvement
--------------------|----------|-----------|-------------
File Size (bytes)   | 19,307   | 3,538     | -82%
Lines of Code       | 118      | 79        | -33%
Estimated Tokens    | 4,826    | 884       | -82%
Execution Time (ms) | 2,045    | 18*       | -99%*

*With cache hit
```

### Step 4: Iterate Until Targets Met

**If Still Not Meeting Targets**:

1. **Too many tokens?**
   - Remove example code (reference instead)
   - Simplify descriptions
   - Use shorter variable names in JSON

2. **Too slow?**
   - Add fast-path for common cases
   - Cache more aggressively
   - Parallelize if possible

3. **Too much duplication?**
   - Consolidate related rules
   - Use single source of truth
   - Reference shared logic

---

## Best Practices

### 1. Always Create -optimized Versions

**Rule**: Never overwrite the original hook

```
Good:
  - hooks/smart-agent-selector.prompt (original, reference)
  - hooks/smart-agent-selector-optimized.prompt (production)

Bad:
  - hooks/smart-agent-selector.prompt (modified, no baseline)
```

**Benefit**: Easy to compare, can revert if needed

### 2. Test with Real-World Scenarios

**Before Deploying**:
```bash
# Test with actual CCPM commands
/ccpm:plan "Add feature X" my-project

# Test with implementation task
/ccpm:work

# Test with multiple files
/ccpm:sync "Completed API design"
```

**Measure**:
- Hook execution time (should be <5s)
- Agent selection accuracy
- No false positives/negatives

### 3. Document Token Budgets

**In Hook File Comments**:
```
You are an intelligent agent selector.

Token Budget:
- Context injection: ~500 tokens
- Available agents list: ~200 tokens
- Selection logic: ~100 tokens
- Response: ~100 tokens
Total target: <5000 tokens
```

### 4. Monitor Performance Over Time

**Create Baseline**:
```bash
# Week 1
./scripts/benchmark-hooks.sh > week1-results.txt

# Week 4
./scripts/benchmark-hooks.sh > week4-results.txt

# Compare
diff week1-results.txt week4-results.txt
```

**Regression Detection**:
- If execution time increases >20%, investigate
- If token count increases >30%, review changes
- If cache hit rate drops <70%, adjust TTL

### 5. Handle Edge Cases Gracefully

**Template**:
```json
{
  "decision": "approve|block",
  "reasoning": "...",
  "fallback": {
    "decision": "approve",
    "reasoning": "Unable to determine, defaulting to approve"
  }
}
```

**Never**:
- ❌ Throw errors
- ❌ Return incomplete JSON
- ❌ Require external files

---

## Examples & Case Studies

### Example 1: Smart Agent Selector Optimization

**Before** (Original):
```
smart-agent-selector.prompt
- 19,307 bytes
- 118 lines
- ~4,826 tokens
- Detailed explanations for every concept
- Multiple example patterns shown in full
- Verbose scoring algorithm explanation
```

**After** (Optimized):
```
smart-agent-selector-optimized.prompt
- 3,538 bytes (-82%)
- 79 lines (-33%)
- ~884 tokens (-82%)
- Concise bullet points
- Reference examples instead
- Inline scoring formula
```

**Key Changes**:
1. Removed 2,000+ words of explanation
2. Moved complex examples to reference section
3. Used JSON structure instead of prose
4. Consolidated redundant sections
5. Added fast-path detection

**Result**: Maintains 100% functionality with 82% fewer tokens

### Example 2: TDD Enforcer Optimization

**Problem**: Hook ran too many file system checks

**Solution**:
```bash
# Before: Check all possible test locations
for pattern in "*.test.*" "*.spec.*" "__tests__/*"; do
  find . -name "$pattern" -path "*$module*"
done
# Result: ~1,500ms for large codebases

# After: Fast-path + simple patterns
if [ -f "$test_path" ]; then
  # Found, return immediately
fi
# Result: <50ms
```

**Improvement**: 96% faster with same accuracy

### Example 3: Cache Hit Optimization

**Problem**: Cache hit rate only 40% (too low)

**Analysis**: TTL too short, agents change during session

**Solution**: Increase TTL from 60 to 300 seconds

```bash
# Before
CACHE_MAX_AGE=60  # 1 minute → 60% miss rate

# After
CACHE_MAX_AGE=300  # 5 minutes → 85% hit rate
```

**Impact**:
- Hit rate improved from 40% to 85%
- Average response time: 2,000ms → 200ms
- User experience: Much smoother workflow

---

## Conclusion

Hook optimization in CCPM focuses on three core principles:

1. **Performance**: Keep hooks fast (<5s) to avoid latency
2. **Efficiency**: Minimize tokens (<5,000 per hook)
3. **Reliability**: Cache intelligently (85-95% hit rates)

By following the optimization strategies and best practices outlined in this skill, you can maintain or improve hook functionality while significantly reducing execution time and token usage, resulting in a better user experience and lower API costs.

### Next Steps

1. **Profile**: Run `./scripts/benchmark-hooks.sh` to establish baseline
2. **Optimize**: Apply techniques from this skill to slow hooks
3. **Test**: Validate with real-world scenarios
4. **Monitor**: Track performance over time
5. **Iterate**: Continuously improve based on metrics

### Resources

- **Hook Benchmarking**: `/scripts/benchmark-hooks.sh`
- **Agent Discovery**: `/scripts/discover-agents-cached.sh`
- **Optimized Hooks**: `/hooks/*-optimized.prompt`
- **CCPM Documentation**: `/docs/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
