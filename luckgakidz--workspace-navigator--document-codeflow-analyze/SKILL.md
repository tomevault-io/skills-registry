---
name: document-codeflow-analyze
description: Analyze source code flow and generate markdown documentation with sequence diagrams. Trace call chains, identify entry points and participants, and detect branching, loops, and async processing. Use when asked to "analyze code flow", "trace processing", "investigate call chain", or analyze program flow. Use when this capability is needed.
metadata:
  author: luckgakidz
---

# Code Flow Analysis Skill

This skill reads source code and writes program flow plus sequence diagrams into markdown files per feature/behavior.

## When to Use This Skill

- Analyzing code flow for a specific feature or file
- Tracing call chains from entry points
- Documenting program execution flow
- Creating code flow diagrams for new functionality

## Prerequisites

- Access to source code of the target project
- `docs/codeflow/` directory exists (create it if missing)

## Step-by-Step Workflows

### Workflow 1: Analyze a Single Feature

1. **Identify target code**
   - Use `#codebase` to understand project structure
   - Use user-specified file/feature as target

2. **Identify entry point**
   - Find processing start (command, event handler, API call, etc.)
   - Read target files with `#readFile`

3. **Trace call chain**
   - Use `#usages` to trace function/method references
   - Use `#search` for related code
   - Record call order

4. **Identify participants**
   - List actors/objects appearing in sequence diagram
   - Organize relationships among classes/modules/external services

5. **Identify branches and loops**
   - Identify conditional branches (if/else, switch)
   - Identify loops (for, while, map)
   - Identify async processing (async/await, Promise)

6. **Generate document**
   - Create Mermaid sequence diagram
   - Write detailed explanation for each step
   - Output to `docs/codeflow/` with `#editFiles`

### Workflow 2: Full Code Flow Map

1. Analyze project structure via `#codebase`
2. Create dependency map of major components (Mermaid `graph` or `classDiagram`)
3. Enumerate all features/behaviors
4. Run Workflow 1 for each feature
5. Create `README.md` (TOC) and `architecture.md` (architecture diagram)

## Output Template

```markdown
# [Feature Name] Code Flow

## Overview
[Concise explanation of what this feature does]

## Related Files
| File | Key Functions/Classes | Role |
|------|-----------------------|------|
| [path/to/file.ts](../../path/to/file.ts) | [ClassName](../../path/to/file.ts#L10), [functionName()](../../path/to/file.ts#L50) | [Role description] |

## Sequence Diagram

\```mermaid
sequenceDiagram
    autonumber
    participant User as User
    participant A as Component A
    participant B as Component B

    User->>A: Action
    activate A
    A->>B: Method call
    activate B
    B-->>A: Return value
    deactivate B

    alt Success
        A-->>User: Success response
    else Error
        A-->>User: Error message
    end
    deactivate A
\```

## Detailed Flow

### Step 1: [Step Name]
- **File**: [path/to/file.ts](../../path/to/file.ts)
- **Function**: [functionName()](../../path/to/file.ts#Lline)
- **Description**: [Detailed explanation]
```

> **Link rule**: Write all file paths and function names as Markdown links using paths relative to `docs/codeflow/`, so readers can jump to source. Add `#Lline` for function links.

## Output Directory Layout

```
docs/codeflow/
├── README.md            # Table of contents / overview
├── architecture.md      # Architecture overview diagram (graph/classDiagram)
├── 01-activation.md     # Per-feature flow (sequenceDiagram)
├── 02-scanning.md
└── ...
```

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Cannot find entry point | Use `#search` for command registration/event listeners |
| Call chain is broken | Reverse lookup callers via `#usages` |
| Async order is unclear | Verify async/await patterns and trace Promise chains |
| Circular references exist | Mention cycle with `Note` and diagram one call cycle only |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luckgakidz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
