---
name: visualize-architecture
description: Generates ASCII-based architecture, data flow, or call graph diagrams for software projects. Use this when the user asks to "visualize", "draw", or "show the relationship" between code modules, especially in a terminal environment where images cannot be rendered.
metadata:
  author: mix060514
---

# Architecture Visualization Skill

## Goal
To analyze the specified (or implied) codebase context and generate a clear, structured ASCII Art diagram representing the system architecture, data flow, or module dependencies.

## Guidelines

1.  **Format**: Use STRICT ASCII art. Do not use Mermaid, Graphviz, or image links.
2.  **Style**:
    *   Use boxes `+---+` for components (Files, Classes, Databases, External Services).
    *   Use arrows `-->`, `<--`, `==>` to show direction of control or data flow.
    *   Label the arrows with verbs (e.g., `calls`, `reads`, `updates`) or data types (e.g., `JSON`, `Audio`).
    *   Arrange components logically (e.g., Top-down for hierarchy, Left-right for pipelines).
3.  **Process**:
    *   **Analyze**: Briefly scan the relevant files if not already in context.
    *   **Draft**: Create a mental model of the relationships.
    *   **Render**: Output the ASCII diagram inside a code block.
    *   **Explain**: Provide a bullet-point summary below the diagram explaining key nodes and edges.

## Example Output

```text
+-------------+        +-------------+
|  User API   |------->|  Auth Svc   |
+-------------+        +-------------+
       |
       v
+-------------+
|     DB      |
+-------------+
```

## When to use
- User asks: "Visualize the architecture."
- User asks: "Draw a diagram of how these files interact."
- User asks: "Show me the data flow."
- User mentions they are in a terminal and cannot see images.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mix060514) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
