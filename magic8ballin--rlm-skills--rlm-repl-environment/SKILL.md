---
name: rlm-repl-environment
description: Technical implementation guide for the RLM REPL environment. Covers code patterns, llm_query usage, answer signaling, and common implementation patterns. Use when this capability is needed.
metadata:
  author: magic8ballin
---

# RLM REPL Environment — The Technical Layer

<role>
You are operating in an RLM REPL Environment. This is a persistent Python environment where you can:

1. Access the `context` variable (contains the entire input as a string)
2. Call `llm_query(prompt)` to invoke sub-LMs recursively
3. Use `print()` to observe truncated outputs and continue reasoning
4. Store intermediate results in variables
5. Signal completion with `FINAL()` or `FINAL_VAR()`
</role>

---

## Environment Initialization

When you enter the REPL, this is already set up:

```python
# Available to you automatically:
context = "... the entire input prompt as a string ..."
# Can be hundreds of millions of characters

def llm_query(prompt: str) -> str:
    """
    Invoke a sub-LM with the given prompt.
    The sub-LM can handle ~500K characters in its context.
    Results are returned as a string.
    
    IMPORTANT: This is a synchronous blocking call.
    Use wisely — each call has cost and latency.
    """
    pass  # Implemented by the environment

# Standard Python available
import re
import json
# etc.
```

---

## The `llm_query()` Function

### Purpose
Recursively invoke a sub-LM on a subset of data. This is your superpower for avoiding Context Rot.

### Capacity
The sub-LM can handle **~500K characters** — use this to your advantage. Don't make tiny calls when you can batch.

### Best Practices

```python
# ✅ GOOD: Batch multiple items per call
chunk = "\n".join(documents[0:50])  # 50 documents at once
result = llm_query(f"Analyze these 50 documents for {query}:\n{chunk}")

# ❌ BAD: One call per item
for doc in documents:  # 1000 documents
    result = llm_query(f"Analyze: {doc}")  # 1000 calls = expensive!
```

### Prompt Template Pattern

```python
# Clear, structured prompts for sub-LMs
finding = llm_query(f"""
ROLE: You are analyzing section {i} of {total_sections}.

TASK: {specific_task_description}

CONTEXT: The full query is "{original_query}". You are handling one piece.

CONTENT TO ANALYZE:
{chunk_content}

OUTPUT FORMAT: 
- If relevant information found: State findings concisely
- If nothing relevant: Reply "No relevant information"
- Do not summarize the content; extract only what's needed
""")
```

---

## The `context` Variable

### What It Contains
The entire input prompt, stored as a string. This can be arbitrarily long — millions or billions of characters.

### How to Access It

```python
# Basic info
print(f"Context length: {len(context)} characters")
print(f"Context type: {type(context)}")

# If context is a list (chunked input)
if isinstance(context, list):
    print(f"Number of chunks: {len(context)}")
    print(f"Chunk sizes: {[len(c) for c in context]}")

# If context is a string (continuous input)
if isinstance(context, str):
    print(f"First 500 chars: {context[:500]}")
    print(f"Last 500 chars: {context[-500:]}")
```

### Golden Rule
**Never try to feed all of `context` into a single `llm_query()` if it exceeds ~500K characters.** That would cause Context Rot in the sub-LM too.

---

## Print Statements

### Purpose
Observe intermediate results. The REPL shows truncated output, allowing you to continue reasoning.

### Pattern
```python
# Exploration
print(f"Found {len(relevant_items)} relevant items")
print(f"Sample: {relevant_items[:3]}")

# Progress tracking
for i, chunk in enumerate(chunks):
    result = llm_query(f"Process chunk {i}...")
    print(f"Chunk {i}/{len(chunks)}: {result[:100]}...")  # Truncate for readability
```

### Warning
Long print outputs are truncated. If you need to analyze a result, store it in a variable and potentially pass it to `llm_query()` for semantic analysis.

---

## Answer Signaling

### `FINAL(answer)`
Use when you can directly state the answer.

```python
# After processing...
answer = "The beauty pageant winner was Maria Dalmacio"
FINAL(answer)
```

### `FINAL_VAR(variable_name)`
Use when you've built up the answer in a variable (especially for long outputs).

```python
# After accumulating results...
accumulated_results = []
for chunk in chunks:
    result = llm_query(f"Process: {chunk}")
    accumulated_results.append(result)

final_output = "\n".join(accumulated_results)
FINAL_VAR(final_output)
```

### Critical Warning
**Do NOT use FINAL() for plans or intentions. Only use it for actual answers.**

```python
# ❌ BAD: This is a plan, not an answer
FINAL("I will now analyze the data and find the answer")

# ✅ GOOD: This is an actual answer
FINAL("The answer is 42, based on analysis of sections 3, 7, and 12")
```

---

## Common Code Patterns

### Pattern 1: Iterative Book Reading
Process a document section by section, accumulating knowledge.

```python
query = "Did Gryffindor win because they were leading?"
buffers = []

sections = context if isinstance(context, list) else context.split('\n\n')

for i, section in enumerate(sections):
    if i == len(sections) - 1:
        # Last section: synthesize with all gathered info
        buffer = llm_query(f"""
        Last section of the book. 
        Previously gathered information: {buffers}
        
        Answer the query: {query}
        
        Section content: {section}
        """)
        print(f"Final synthesis: {buffer}")
    else:
        # Middle sections: gather relevant info
        buffer = llm_query(f"""
        Section {i+1} of {len(sections)}.
        Gather any information relevant to: {query}
        
        Section content: {section}
        """)
        if "relevant" in buffer.lower() or "found" in buffer.lower():
            buffers.append(buffer)
            print(f"Section {i+1}: {buffer[:100]}...")
```

### Pattern 2: Chunk and Query (Map-Reduce)
For aggregation tasks.

```python
query = "How many jobs did the author of The Great Gatsby have?"

# Chunk the context
chunk_count = 10
chunk_size = len(context) // chunk_count
answers = []

for i in range(chunk_count):
    start = i * chunk_size
    end = (i + 1) * chunk_size if i < chunk_count - 1 else len(context)
    chunk_str = context[start:end]
    
    answer = llm_query(f"""
    Try to answer this question: {query}
    
    Documents (chunk {i+1} of {chunk_count}):
    {chunk_str}
    
    If you find relevant information, state it.
    If not, say "No information found in this chunk."
    """)
    answers.append(f"Chunk {i+1}: {answer}")
    print(f"Chunk {i+1} result: {answer[:100]}...")

# Aggregate
final_answer = llm_query(f"""
Question: {query}

Answers from {chunk_count} document chunks:
{chr(10).join(answers)}

Synthesize these into a final answer.
""")
FINAL(final_answer)
```

### Pattern 3: Semantic Chunking
Split by logical boundaries, not arbitrary positions.

```python
import re

# Split by markdown headers
sections = re.split(r'\n(?=#{1,3}\s)', context)

buffers = []
for i, section in enumerate(sections):
    # Get header
    header_match = re.match(r'^(#{1,3}\s+.+)', section)
    header = header_match.group(1) if header_match else f"Section {i}"
    
    summary = llm_query(f"Summarize this {header} section:\n{section[:500000]}")  # Cap at 500K
    buffers.append(f"{header}: {summary}")

# Final synthesis
final_answer = llm_query(f"""
Based on these section summaries, answer: {query}

Summaries:
{chr(10).join(buffers)}
""")
FINAL(final_answer)
```

### Pattern 4: Targeted Extraction
When you know what you're looking for.

```python
import re

# Use keyword search to find relevant chunks
keyword = "La Union"
pattern = re.compile(re.escape(keyword), re.IGNORECASE)

# Find all matches with surrounding context
matches = []
for match in pattern.finditer(context):
    start = max(0, match.start() - 2000)  # 2K chars before
    end = min(len(context), match.end() + 2000)  # 2K chars after
    matches.append(context[start:end])

print(f"Found {len(matches)} matches for '{keyword}'")

# Process only relevant chunks
findings = []
for i, chunk in enumerate(matches):
    finding = llm_query(f"""
    This text contains a mention of "{keyword}".
    Extract any information relevant to the query: {query}
    
    Text: {chunk}
    """)
    findings.append(finding)

# Synthesize findings
final = llm_query(f"Combine these findings about {keyword}:\n{chr(10).join(findings)}")
FINAL(final)
```

### Pattern 5: Verification Loop
Double-check answers before returning.

```python
# First pass: get an answer
raw_answer = llm_query(f"Based on the data, answer: {query}\n\nData: {relevant_chunks}")

# Verification pass: confirm with evidence
verified = llm_query(f"""
PROPOSED ANSWER: {raw_answer}

EVIDENCE USED: {relevant_chunks[:100000]}  # Cap for sub-LM

Question: Is the proposed answer correct based on this evidence?
- If correct, respond with the answer
- If incorrect, provide the correct answer
- If uncertain, state what additional information is needed
""")

FINAL(verified)
```

---

## Error Handling

```python
# Graceful degradation
try:
    result = llm_query(f"Process: {chunk}")
except Exception as e:
    print(f"Sub-query failed: {e}")
    result = "ERROR: Unable to process chunk"

# Size checks before calling
if len(chunk) > 500000:
    print(f"Warning: Chunk too large ({len(chunk)} chars), splitting...")
    sub_chunks = [chunk[i:i+400000] for i in range(0, len(chunk), 400000)]
    results = [llm_query(f"Process: {sc}") for sc in sub_chunks]
    result = "\n".join(results)
```

---

## Model-Specific Considerations

### GPT-5 Class Models
- Conservative with sub-queries
- Good at strategic chunking
- Trust their judgment on when to call `llm_query()`

### Qwen/Open Models
- May make excessive sub-queries (thousands!)
- Add explicit batching instructions:
  ```
  IMPORTANT: Batch ~200K characters per llm_query call.
  Do NOT call llm_query for every line — this is expensive.
  ```

---

## Integration with Related Skills

**Parent Skill:** `rlm-orchestrator/SKILL.md` — Provides the strategic layer

**Sibling Skill:** `rlm-context-scout/SKILL.md` — Provides reconnaissance patterns

**This Skill:** The technical execution layer

```
Orchestrator (Strategy) 
    → Scout (Reconnaissance) 
    → REPL Environment (Execution) 
    → Answer Signaling (Completion)
```

---

## REPL Session Example

```python
# === RLM REPL SESSION ===

# Step 1: Probe structure
print(f"Context length: {len(context)} chars")  # 8.3M chars
print(f"First 500: {context[:500]}")  # Understand format

# Step 2: Reconnaissance
import re
keywords = ["festival", "La Union", "beauty", "pageant"]
for kw in keywords:
    count = len(re.findall(re.escape(kw), context, re.IGNORECASE))
    print(f"'{kw}': {count} occurrences")

# Step 3: Targeted extraction
la_union_chunks = []
for match in re.finditer(r'La Union', context, re.IGNORECASE):
    start = max(0, match.start() - 5000)
    end = min(len(context), match.end() + 5000)
    la_union_chunks.append(context[start:end])

print(f"Extracted {len(la_union_chunks)} chunks around 'La Union'")

# Step 4: Process relevant chunks
findings = []
for i, chunk in enumerate(la_union_chunks[:5]):  # Top 5
    finding = llm_query(f"""
    Find: Who won the beauty pageant mentioned with La Union festival?
    
    Context: {chunk}
    """)
    findings.append(finding)
    print(f"Chunk {i}: {finding}")

# Step 5: Synthesize and verify
answer = llm_query(f"""
Question: Who won the beauty pageant at the La Union festival?

Findings from relevant chunks:
{chr(10).join(findings)}

Provide the name and any verification.
""")

# Step 6: Signal completion
FINAL(answer)
```

---

*This is your workbench. Use the tools wisely.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/magic8ballin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
