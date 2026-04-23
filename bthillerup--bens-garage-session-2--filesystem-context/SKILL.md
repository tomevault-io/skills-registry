---
name: filesystem-context
description: This skill should be used when the user asks to "offload context to files", "implement dynamic context discovery", "use filesystem for agent memory", "reduce context window bloat", or mentions file-based context management or just-in-time context loading. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Filesystem-Based Context Engineering

The filesystem provides a single interface through which agents can flexibly store, retrieve, and update effectively unlimited context. Files enable **dynamic context discovery**: agents pull relevant context on demand rather than carrying everything.

## When to Activate

Activate this skill when:
- Tool outputs are bloating the context window
- Agents need to persist state across long trajectories
- Sub-agents must share information without direct message passing
- Tasks require more context than fits in the window

## Core Patterns

### Pattern 1: Filesystem as Scratch Pad

Write large tool outputs to files instead of returning directly to context.

```python
def handle_tool_output(output: str, threshold: int = 2000) -> str:
    if len(output) < threshold:
        return output
    
    file_path = f"scratch/{tool_name}_{timestamp}.txt"
    write_file(file_path, output)
    
    key_summary = extract_summary(output, max_tokens=200)
    return f"[Output in {file_path}. Summary: {key_summary}]"
```

### Pattern 2: Plan Persistence

Write plans to filesystem. Agent re-reads to remind itself of objectives.

```yaml
# scratch/current_plan.yaml
objective: "Refactor authentication module"
status: in_progress
steps:
  - id: 1
    description: "Audit current auth endpoints"
    status: completed
  - id: 2
    description: "Design new token validation"
    status: in_progress
```

### Pattern 3: Sub-Agent Communication via Filesystem

Sub-agents write findings directly. Coordinator reads files, bypassing message passing.

```
workspace/
  agents/
    research_agent/
      findings.md
      sources.jsonl
    code_agent/
      changes.md
      test_results.txt
  coordinator/
    synthesis.md
```

### Pattern 4: Dynamic Skill Loading

Store skills as files. Include only names/descriptions in static context.

```
Available skills (load with read_file when relevant):
- database-optimization: Query tuning and indexing
- api-design: REST/GraphQL best practices
```

### Pattern 5: Terminal and Log Persistence

Sync terminal output to files. Agent greps for relevant sections.

```bash
grep -A 5 "error" terminals/1.txt
```

### Pattern 6: Learning Through Self-Modification

Agents write learned information to their own instruction files.

```python
def remember_preference(key: str, value: str):
    prefs = load_yaml("agent/user_preferences.yaml")
    prefs[key] = value
    write_yaml("agent/user_preferences.yaml", prefs)
```

## Filesystem Search Techniques

Combine these for comprehensive discovery:

- `ls` / `list_dir`: Discover directory structure
- `glob`: Find files matching patterns (`**/*.py`)
- `grep`: Search file contents, returns matching lines
- `read_file` with ranges: Read specific lines without loading entire files

This combination often outperforms semantic search for technical content.

## File Organization

```
project/
  scratch/           # Temporary working files
    tool_outputs/    # Large tool results
    plans/           # Active plans and checklists
  memory/            # Persistent learned information
    preferences.yaml
    patterns.md
  skills/            # Loadable skill definitions
  agents/            # Sub-agent workspaces
```

## When to Use

**Use filesystem patterns when**:
- Tool outputs exceed 2000 tokens
- Tasks span multiple conversation turns
- Multiple agents need to share state
- Skills/instructions exceed system prompt space

**Avoid when**:
- Tasks complete in single turns
- Context fits comfortably in window
- Latency is critical (file I/O adds overhead)

## Guidelines

1. Write large outputs to files; return summaries to context
2. Store plans in structured files for re-reading
3. Use sub-agent file workspaces instead of message chains
4. Load skills dynamically rather than stuffing all into system prompt
5. Combine grep/glob with semantic search for comprehensive discovery
6. Implement cleanup for scratch files to prevent unbounded growth

---

**Created**: 2026-01-07 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
