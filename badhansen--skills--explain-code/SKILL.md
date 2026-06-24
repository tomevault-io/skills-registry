---
name: explain-code
description: Explain source code using structured text explanations and Excalidraw diagrams. Use this skill when users ask to visualize code, explain code architecture, create class diagrams, flow diagrams, ER diagrams, feature diagrams, component diagrams, sequence diagrams, or any software engineering visualization from source code. Also use when documenting codebases, understanding complex logic, debugging workflows, or creating technical documentation. Outputs `.excalidraw` JSON files editable at excalidraw.com or embeddable via @excalidraw/excalidraw npm package. Use when this capability is needed.
metadata:
  author: badhansen
---

# Code Visualizer with Excalidraw

Explain source code through structured text explanations and hand-drawn style Excalidraw diagrams.

## About Excalidraw

- Open source at excalidraw.com and GitHub
- Hand-drawn/sketchy aesthetic
- Real-time collaboration support
- Browser-based, no sign-up required
- `.excalidraw` JSON format
- Embeddable via `@excalidraw/excalidraw` npm package

## Workflow

1. **Receive** code input with optional context
2. **Analyze** code structure, patterns, and flow
3. **Select** appropriate diagram type(s)
4. **Generate** text explanation with key insights
5. **Create** `.excalidraw` JSON diagram
6. **Output** to user's working directory or specified location

## Input Handling

Accept code in these formats:

```
<code language="python">
def example():
    pass
</code>
```

Or with context:

```
<code language="typescript" context="Authentication module for REST API">
...
</code>
```

Or request type:

```
<request type="class_diagram">
Visualize the User authentication system
</request>
```

## Code Analysis

### Parse and Identify

| Code Element | What to Extract                        |
| ------------ | -------------------------------------- |
| Functions    | Name, params, return type, calls       |
| Classes      | Name, methods, properties, inheritance |
| Modules      | Imports, exports, dependencies         |
| APIs         | Endpoints, methods, request/response   |
| Data         | Models, schemas, transformations       |

### Identify Patterns

- Control flow (conditionals, loops, error handling)
- Data flow (input → processing → output)
- Design patterns (singleton, factory, observer, etc.)
- Architecture patterns (MVC, layered, microservices)

## Diagram Selection

| Code Characteristic     | Recommended Diagram  |
| ----------------------- | -------------------- |
| OOP with inheritance    | Class Diagram        |
| Process/algorithm logic | Flow Diagram         |
| Database models         | ER Diagram           |
| System modules          | Component Diagram    |
| API interactions        | Sequence Diagram     |
| Feature breakdown       | Feature Diagram      |
| State management        | State Diagram        |
| High-level overview     | Architecture Diagram |

Multiple diagrams may be appropriate for complex code.

## Explanation Structure

Every code explanation includes:

### 1. High-Level Overview

Brief summary of what the code does and its purpose.

### 2. Key Components

List main classes, functions, or modules with roles.

### 3. Step-by-Step Logic

Walk through the execution flow with numbered steps.

### 4. Data Flow

Describe how data moves through the system.

### 5. Design Decisions

Note patterns used and architectural choices.

### 6. Performance Notes (if relevant)

Complexity, bottlenecks, optimization opportunities.

### 7. Potential Improvements (if relevant)

Suggestions for refactoring or enhancement.

## Excalidraw JSON Structure

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "code-visualizer",
  "elements": [...],
  "appState": {
    "gridSize": 20,
    "viewBackgroundColor": "#ffffff"
  },
  "files": {}
}
```

## Element Reference

> **CRITICAL**: Every element MUST include these base properties for proper rendering:
>
> ```json
> {
>   "version": 1, "versionNonce": 1, "isDeleted": false,
>   "seed": <unique_number>, "angle": 0, "opacity": 100,
>   "groupIds": [], "frameId": null, "boundElements": [],
>   "updated": 1, "link": null, "locked": false
> }
> ```
>
> Text elements also need: `containerId`, `originalText`, `lineHeight`, `verticalAlign`
> Lines/arrows also need: `lastCommittedPoint`, `startBinding`, `endBinding`, `startArrowhead`, `endArrowhead`
>
> See [references/diagram-templates.md](references/diagram-templates.md) for complete examples.

### Shapes

**Rectangle** (classes, entities, processes) - key properties:

```json
{
    "type": "rectangle",
    "id": "rect_1",
    "x": 100,
    "y": 100,
    "width": 200,
    "height": 120,
    "strokeColor": "#1e1e1e",
    "backgroundColor": "#a5d8ff",
    "fillStyle": "solid",
    "strokeWidth": 2,
    "roughness": 1,
    "roundness": { "type": 3 }
}
```

**Diamond** (decisions) - key properties:

```json
{
    "type": "diamond",
    "id": "diamond_1",
    "x": 150,
    "y": 200,
    "width": 100,
    "height": 80,
    "backgroundColor": "#ffec99"
}
```

**Ellipse** (start/end, actors) - key properties:

```json
{
    "type": "ellipse",
    "id": "ellipse_1",
    "x": 100,
    "y": 100,
    "width": 80,
    "height": 80,
    "backgroundColor": "#b2f2bb"
}
```

### Connectors

**Arrow** (relationships, flow) - key properties:

```json
{
    "type": "arrow",
    "id": "arrow_1",
    "x": 300,
    "y": 160,
    "points": [
        [0, 0],
        [100, 0]
    ],
    "strokeWidth": 2,
    "endArrowhead": "arrow"
}
```

**Line** (connections, dividers) - key properties:

```json
{
    "type": "line",
    "id": "line_1",
    "x": 100,
    "y": 145,
    "points": [
        [0, 0],
        [200, 0]
    ],
    "strokeWidth": 1
}
```

### Text

Key properties (must also include text-specific required props):

```json
{
    "type": "text",
    "id": "text_1",
    "x": 110,
    "y": 110,
    "text": "ClassName",
    "fontSize": 16,
    "fontFamily": 1,
    "textAlign": "center",
    "originalText": "ClassName",
    "lineHeight": 1.25,
    "verticalAlign": "top",
    "containerId": null
}
```

## Color Palette

| Element Type  | Background | Meaning            |
| ------------- | ---------- | ------------------ |
| Classes       | `#a5d8ff`  | Primary code units |
| Interfaces    | `#d0bfff`  | Abstract contracts |
| Entities/Data | `#b2f2bb`  | Data structures    |
| Decisions     | `#ffec99`  | Branch points      |
| Start/End     | `#ffc9c9`  | Boundaries         |
| Processes     | `#e9ecef`  | Actions            |
| External      | `#ffd8a8`  | APIs, services     |
| Errors        | `#ff8787`  | Exception paths    |

## Relationship Arrows

| Relationship   | Arrowhead         | Style        |
| -------------- | ----------------- | ------------ |
| Association    | `arrow`           | solid        |
| Inheritance    | `triangle`        | solid        |
| Implementation | `triangle`        | dashed       |
| Dependency     | `arrow`           | dashed       |
| Composition    | `diamond` (start) | solid        |
| Aggregation    | `diamond_outline` | solid        |
| Data flow      | `arrow`           | solid        |
| Return         | `arrow`           | dashed, gray |

## Layout Guidelines

1. **Grid**: Align to 100px grid
2. **Spacing**: Minimum 50px between elements
3. **Flow**: Left-to-right or top-to-bottom
4. **Grouping**: Cluster related components
5. **Hierarchy**: Important elements at top/left
6. **Labels**: Center text, 10px padding from edges

## ID Generation

Format: `{type}_{index}_{random5}`

```python
import random, string
def gen_id(t, i):
    return f"{t}_{i}_{''.join(random.choices(string.ascii_lowercase+string.digits,k=5))}"
```

## Output Format

For each visualization:

1. **Text explanation** in conversation
2. **Diagram file** saved to `./{name}.excalidraw` or user-specified path
3. **Usage instructions** for opening/editing

Example naming: `auth-system-class-diagram.excalidraw`

## Detailed Patterns

For specific diagram patterns, templates, and complete examples, see:

- [references/diagram-templates.md](references/diagram-templates.md) - Element templates
- [references/code-patterns.md](references/code-patterns.md) - Code-to-diagram mapping

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/badhansen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
