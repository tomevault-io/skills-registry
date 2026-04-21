---
name: diagram
description: Create PlantUML diagrams and add them to documentation Use when this capability is needed.
metadata:
  author: bobcob7
---

Create a PlantUML diagram and optionally add it to a markdown file.

## Usage

- `/diagram sequence user login flow` - Create a sequence diagram
- `/diagram component for README.md` - Create a component diagram and add to README
- `/diagram class data models` - Create a class diagram

## Steps

1. **Determine diagram type** from arguments:
   - `sequence` - Interactions between actors/systems
   - `component` - System architecture and dependencies
   - `class` - Data models and relationships
   - `activity` - Workflows and decision flows
   - `state` - State machines

2. **Create the PlantUML syntax** appropriate for the type

3. **Generate URLs** using the plantuml plugin MCP server:
   ```bash
   cd /path/to/plugins/plantuml && printf '%s\n%s\n%s\n' \
     '{"jsonrpc":"2.0","id":0,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"claude","version":"1.0"}}}' \
     '{"jsonrpc":"2.0","method":"notifications/initialized"}' \
     '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"plantuml_generate_urls","arguments":{"diagram":"..."}}}' \
     | python3 main.py
   ```

4. **If a target file is specified**, add the diagram to it:
   - Insert image as a clickable link to the editor: `[![description](png-url)](editor-url)`
   - The alt text should be a human-friendly summary of what the diagram shows
   - The PNG renders inline in documentation; clicking it opens the PlantUML editor
   - Do NOT add `<details>` blocks or other non-standard markdown

5. **Show the user**:
   - The generated URLs (png, svg, editor)
   - The PlantUML source code

## Diagram Templates

### Sequence
```plantuml
@startuml
actor User
participant "System A" as A
participant "System B" as B

User -> A: request
A -> B: process
B --> A: response
A --> User: result
@enduml
```

### Component
```plantuml
@startuml
package "System" {
  [Component A] as A
  [Component B] as B
}
A --> B : uses
@enduml
```

### Class
```plantuml
@startuml
class Entity {
  +id: string
  +name: string
  +save(): void
}
@enduml
```

## Output Format

When adding to a file, use a linked image with descriptive alt text. Do NOT include `<details>` blocks or inline PlantUML source — the editor link serves as the source reference:
```markdown
[![Human-friendly description of what the diagram shows](https://www.plantuml.com/plantuml/png/~1...)](https://www.plantuml.com/plantuml/uml/~1...)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bobcob7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
