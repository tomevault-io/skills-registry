---
name: visual-communicator
description: Enforces Visual Communication Standards. Use this skill when explaining complex logic, architectures, workflows, or when the user asks for diagrams. Use when this capability is needed.
metadata:
  author: mr-phariyawit
---

# Visual Communicator Skill

## Purpose
To ensure that complex ideas are always accompanied by visual representations using Mermaid diagrams, adhering to the standard defined in `GEMINI.md`.

## Trigger Conditions
- User asks for an explanation of code, architecture, or flow.
- You are writing a `spec` or `implementation_plan`.
- User explicitly asks for a diagram.

## Decision Logic: Which Diagram?

| Scenario | Diagram Type | Syntax |
| :--- | :--- | :--- |
| **Logic/Process/Decision Tree** | Flowchart | `graph TD` |
| **API Calls/Protocol/Interactions** | Sequence | `sequenceDiagram` |
| **Data Models/Classes/DB Schema** | Class | `classDiagram` |
| **State Changes/Lifecycle** | State | `stateDiagram-v2` |
| **Timeline/Roadmap** | Gantt | `gantt` |

## Execution Rules
1.  **Always Visualize**: Do not just explain with text; show it with a diagram first.
2.  **Syntax Safety**:
    -   Quote labels with special chars: `id["Label (Info)"]`.
    -   Avoid HTML in labels if possible.
3.  **Context Placement**:
    -   If writing an Artifact (markdown), embed the diagram directly.
    -   If chatting, provide the diagram in the chat response.

## Example Templates

### Flowchart
```mermaid
graph TD
    A[Start] --> B{Condition?}
    B -- Yes --> C[Process]
    B -- No --> D[End]
```

### Sequence
```mermaid
sequenceDiagram
    participant U as User
    participant S as Server
    U->>S: Request
    S-->>U: Response
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mr-phariyawit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
