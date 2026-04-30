---
name: langsmith-fetch
description: Debug LangChain and LangGraph agents by fetching execution traces from LangSmith Studio. Use when debugging agent behavior, investigating errors, analyzing tool calls, checking memory operations, or e Use when this capability is needed.
metadata:
  author: ranbot-ai
---


# LangSmith Fetch - Agent Debugging Skill

Debug LangChain and LangGraph agents by fetching execution traces directly from LangSmith Studio in your terminal.

## When to Use This Skill

Automatically activate when user mentions:
- 🐛 "Debug my agent" or "What went wrong?"
- 🔍 "Show me recent traces" or "What happened?"
- ❌ "Check for errors" or "Why did it fail?"
- 💾 "Analyze memory operations" or "Check LTM"
- 📊 "Review agent performance" or "Check token usage"
- 🔧 "What tools were called?" or "Show execution flow"

## Prerequisites

### 1. Install langsmith-fetch
```bash
pip install langsmith-fetch
```

### 2. Set Environment Variables
```bash
export LANGSMITH_API_KEY="your_langsmith_api_key"
export LANGSMITH_PROJECT="your_project_name"
```

**Verify setup:**
```bash
echo $LANGSMITH_API_KEY
echo $LANGSMITH_PROJECT
```

## Core Workflows

### Workflow 1: Quick Debug Recent Activity

**When user asks:** "What just happened?" or "Debug my agent"

**Execute:**
```bash
langsmith-fetch traces --last-n-minutes 5 --limit 5 --format pretty
```

**Analyze and report:**
1. ✅ Number of traces found
2. ⚠️ Any errors or failures
3. 🛠️ Tools that were called
4. ⏱️ Execution times
5. 💰 Token usage

**Example response format:**
```
Found 3 traces in the last 5 minutes:

Trace 1: ✅ Success
- Agent: memento
- Tools: recall_memories, create_entities
- Duration: 2.3s
- Tokens: 1,245

Trace 2: ❌ Error
- Agent: cypher
- Error: "Neo4j connection timeout"
- Duration: 15.1s
- Failed at: search_nodes tool

Trace 3: ✅ Success
- Agent: memento
- Tools: store_memory
- Duration: 1.8s
- Tokens: 892

💡 Issue found: Trace 2 failed due to Neo4j timeout. Recommend checking database connection.
```

---

### Workflow 2: Deep Dive Specific Trace

**When user provides:** Trace ID or says "investigate that error"

**Execute:**
```bash
langsmith-fetch trace <trace-id> --format json
```

**Analyze JSON and report:**
1. 🎯 What the agent was trying to do
2. 🛠️ Which tools were called (in order)
3. ✅ Tool results (success/failure)
4. ❌ Error messages (if any)
5. 💡 Root cause analysis
6. 🔧 Suggested fix

**Example response format:**
```
Deep Dive Analysis - Trace abc123

Goal: User asked "Find all projects in Neo4j"

Execution Flow:
1. ✅ search_nodes(query: "projects")
   → Found 24 nodes

2. ❌ get_node_details(node_id: "proj_123")
   → Error: "Node not found"
   → This is the failure point

3. ⏹️ Execution stopped

Root Cause:
The search_nodes tool returned node IDs that no longer exist in the database,
possibly due to recent deletions.

Suggested Fix:
1. Add error handling in get_node_details tool
2. Filter deleted nodes in search results
3. Update cache invalidation strategy

Token Usage: 1,842 tokens ($0.0276)
Execution Time: 8.7 seconds
```

---

### Workflow 3: Export Debug Session

**When user says:** "Save this session" or "Export traces"

**Execute:**
```bash
# Create session folder with timestamp
SESSION_DIR="langsmith-debug/session-$(date +%Y%m%d-%H%M%S)"
mkdir -p "$SESSION_DIR"

# Export traces
langsmith-fetch traces "$SESSION_DIR/traces" --last-n-minutes 30 --limit 50 --include-metadata

# Export threads (conversations)
langsmith-fetch threads "$SESSION_DIR/threads" --limit 20
```

**Report:**
```
✅ Session exported successfully!

Location: langsmith-debug/session-20251224-143022/
- Traces: 42 files
- Threads: 8 files

You can now:
1. Review individual trace files
2. Share folder with team
3. Analyze with external tools
4. Archive for future reference

Session size: 2.3 MB
```

---

### Workflow 4: Error Detection

**When user asks:** "Show me errors" or "What's failing?"

**Execute:**
```bash
# Fetch recent traces
langsmith-fetch traces --last-n-minutes 30 --limit 50 --format json > recent-traces.json

# Search for errors
grep -i "error\|failed\|exception" recent-traces.json
```

**Analyze and report:**
1. 📊 Total errors found
2. ❌ Error types and frequency
3. 🕐 When errors occurred
4. 🎯 Which agents/tools failed
5. 💡 Common patterns

**Example response format:**
```
Error Analysis - Last 30 Minutes

Total Traces: 50
Failed Traces: 7 (14% failure rate)

Error Breakdown:
1. Neo4j Connection Timeout (4 occurrences)
   - Agent: cypher
   - Tool: search_nodes
   - First occurred: 14:32
   - Last occurred: 14:45
   - Pattern: Happens during peak load

2. Memory Store Failed (2 occurrences)
   - Agent: memento
   - Tool: store_memory
   - Error: "Pinecone rate limit exceeded"
   - Occurred: 14:38, 14:41

3. Tool Not Found (1 occurrence)
   - Agent: sqlcrm
   - Attempted tool: "export_report" (doesn't exist)
   - Occurred: 14:35

💡 Recommendations:
1. Add retry logic for Neo4j timeouts
2. Implement rate limiting for Pinecone
3. Fix sqlcrm tool configuration
```

---

## Common Use Cases

### Use Case 1: "Agent Not Responding"

**User says:** "My agent isn't doing anything"

**Steps:**
1. Check if traces exist:
   ```bash
   langsmith-fetch traces --last-n-minutes 5 --limit 5
   ```

2. **If NO traces found:**
   - Tracing migh

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ranbot-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
