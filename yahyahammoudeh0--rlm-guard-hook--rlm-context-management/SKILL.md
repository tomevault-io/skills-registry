---
name: rlm-context-management
description: This skill implements the **Recursive Language Model (RLM)** approach from MIT CSAIL (arXiv:2512.24601) for handling contexts that exceed effective processing capacity. Instead of feeding long prompts directly into the neural network, RLM treats them as external environment data to be examined programmatically. Use when this capability is needed.
metadata:
  author: yahyahammoudeh0
---
---
name: rlm-context-management
description: RLM (Recursive Language Model) approach from MIT CSAIL for handling long contexts. Treats large inputs as external data, probes before processing, chunks intelligently, spawns subagents for fresh context, and aggregates results. Use for multi-file operations, large codebases, or when context rot is observed.
---

# RLM Context Management Skill

## Overview

This skill implements the **Recursive Language Model (RLM)** approach from MIT CSAIL (arXiv:2512.24601) for handling contexts that exceed effective processing capacity. Instead of feeding long prompts directly into the neural network, RLM treats them as external environment data to be examined programmatically.

## Core Principle

> "The key insight is that long prompts should be treated as part of the external environment rather than fed directly into the neural network."

Traditional approach: `LLM(long_prompt)` -> context rot, forgetting, inconsistency

RLM approach: `LLM(code_to_examine(long_prompt))` -> structured, reliable, scalable

---

## The RLM REPL Pattern

Think of yourself as operating in a Python REPL where:
- Large context is stored in a `context` variable (files, documents, data)
- You write code to examine, filter, and process this context
- You can recursively call yourself (`llm_query()`) on smaller chunks
- You use `FINAL()` or `FINAL_VAR()` to return results

### Claude Code Implementation

```
context = Files accessible via Read, Glob, Grep
print(context[:1000]) = Read with offset=0, limit=50
len(context) = Glob to count files, wc -l for lines
llm_query(prompt) = Task tool with subagent
FINAL(answer) = Direct response to user
FINAL_VAR(data) = Write to file
```

---

## Methodology

### Phase 1: Probe Before Processing

**Never read entire large files.** First understand structure:

```
# Discover what exists
Glob("**/*.py") -> list of files
Glob("**/test_*.py") -> test files specifically

# Sample content
Grep(pattern="class ", type="py") -> find class definitions
Grep(pattern="def ", path="specific_file.py") -> functions in file

# Examine structure
Read(file, limit=50) -> first 50 lines only
Read(file, offset=100, limit=50) -> lines 100-150
```

### Phase 2: Chunk by Structure

Break data into logical units:

| Data Type | Chunking Strategy |
|-----------|-------------------|
| Code files | By class/function using AST or regex |
| Documents | By section headers or paragraphs |
| JSON/Data | By top-level keys or array elements |
| Logs | By time windows or error types |
| Multiple files | Each file as separate chunk |

### Phase 3: Process with Fresh Context

For each chunk, spawn a subagent with clean context:

```
Task(
  subagent_type="general-purpose",
  prompt=f"""
  Analyze this code chunk and extract:
  - Function names and signatures
  - Dependencies imported
  - Key logic patterns

  CHUNK:
  {chunk_content}
  """
)
```

**Why subagents work**: Each subagent starts fresh without accumulated context rot from previous chunks.

### Phase 4: Aggregate Results

Combine subagent outputs with another clean-context call if needed:

```
Task(
  subagent_type="general-purpose",
  prompt=f"""
  Synthesize these analysis results into a coherent summary:

  RESULTS:
  {json.dumps(all_chunk_results)}
  """
)
```

---

## Task-Specific Patterns

### Pattern: Code Analysis

```python
# 1. Discover
files = Glob("src/**/*.ts")

# 2. For each file, spawn analyzer
for file in files:
    result = Task(prompt=f"Analyze {file}: find exports, dependencies, complexity")
    results.append(result)

# 3. Aggregate
Task(prompt=f"Create architecture diagram from: {results}")
```

### Pattern: Document Search (Needle in Haystack)

```python
# 1. Probe structure
sections = Grep(pattern="^#+ ", path="large_doc.md")  # Find headers

# 2. Binary search relevant sections
relevant = Task(prompt=f"Which sections likely contain X? Headers: {sections}")

# 3. Deep dive only relevant parts
for section in relevant:
    Read(file, offset=section.start, limit=section.length)
```

### Pattern: Aggregation Tasks (Counting, Statistics)

```python
# 1. Chunk data
chunks = split_by_structure(data)

# 2. Map: count in each chunk
counts = []
for chunk in chunks:
    count = Task(prompt=f"Count occurrences of X in: {chunk}")
    counts.append(count)

# 3. Reduce: sum results
total = sum(counts)
```

### Pattern: Multi-File Refactoring

```python
# 1. Understand dependencies
deps = Task(prompt="Map import relationships across these files")

# 2. Plan changes with dependency awareness
plan = Task(prompt=f"Plan refactoring order given dependencies: {deps}")

# 3. Execute changes file by file
for file in plan.order:
    Task(prompt=f"Apply refactoring to {file} following plan: {plan.changes[file]}")
```

---

## Context Rot Detection

Watch for these symptoms:
- Contradicting earlier statements
- Forgetting information from early in conversation
- Inconsistent naming or references
- Declining response quality
- Missing obvious connections

**When detected**: Immediately spawn subagent to process current task with fresh context.

---

## The RLM Processor Agent

For heavy-lifting tasks, spawn the dedicated RLM processor:

```
Task(
  subagent_type="rlm-processor",
  prompt="Process this large codebase and find all security vulnerabilities"
)
```

The RLM processor agent (Opus model) will:
1. Probe structure automatically
2. Chunk intelligently
3. Spawn its own subagents for processing
4. Aggregate and return coherent results

---

## Best Practices

1. **Probe First**: Always understand structure before reading content
2. **Limit Reads**: Use offset/limit parameters; never read entire large files
3. **Fresh Context for Analysis**: Subagents prevent context accumulation
4. **Structure-Aware Chunking**: Chunks should be semantically meaningful
5. **Incremental Aggregation**: Build answers progressively, not all at once
6. **Monitor for Rot**: If quality degrades, spawn fresh subagent

---

## Example Prompts for Subagents

### Code Chunk Analysis
```
Analyze this code chunk. Return JSON with:
- exports: list of exported functions/classes
- imports: list of dependencies
- complexity: low/medium/high
- summary: 2-3 sentence description

CODE:
{chunk}
```

### Document Section Search
```
Search this document section for information about {topic}.
Return:
- found: true/false
- relevant_quotes: list of exact quotes
- context: surrounding information

SECTION:
{section_content}
```

### Result Aggregation
```
Synthesize these partial results into a coherent final answer.
Resolve any contradictions by preferring more specific information.

PARTIAL RESULTS:
{results}

ORIGINAL QUESTION:
{question}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yahyahammoudeh0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
