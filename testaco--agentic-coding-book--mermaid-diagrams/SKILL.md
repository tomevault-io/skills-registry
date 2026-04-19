---
name: mermaid-diagram-generator
description: Generate, validate, and refine Mermaid diagrams (flowcharts, sequence diagrams, class diagrams, state diagrams, Gantt charts, ERD, component diagrams). Use when creating visualizations, documenting workflows, system architectures, or data flows. Includes syntax validation and best practices guidance. Use when this capability is needed.
metadata:
  author: testaco
---

# Mermaid Diagram Generator

## Overview

This skill provides comprehensive support for creating, validating, and refining Mermaid diagrams. Use it whenever you need to visualize workflows, architectures, processes, or relationships.

## Supported Diagram Types

- **Flowcharts**: Decision flows, process steps, algorithm visualization
- **Sequence Diagrams**: Interaction flows, API calls, multi-party conversations
- **Class Diagrams**: Object-oriented design, relationships, inheritance
- **State Diagrams**: State machines, lifecycle processes
- **Entity Relationship Diagrams (ERD)**: Database schemas, data relationships
- **Gantt Charts**: Project timelines, dependencies
- **Pie Charts**: Distribution and composition
- **Graph/Network Diagrams**: Node relationships and connections
- **Component Diagrams**: System architecture and component relationships
- **User Journey**: User flow and experience mapping

## Workflow

### Step 1: Understand the Request
- Identify what needs to be visualized
- Determine the most appropriate diagram type
- Consider the level of detail needed (high-level vs detailed)

### Step 2: Choose Detail Level
- **High-level**: Overview with major components only (3-7 nodes)
- **Medium**: Standard detail with key relationships (8-15 nodes)
- **Detailed**: Comprehensive with all components (15+ nodes)
- **Minimal**: Simplest possible representation (2-5 nodes)

### Step 3: Design the Diagram
- Create clear, well-formatted Mermaid syntax
- Use meaningful node IDs and labels
- Organize logically (top-to-bottom, left-to-right)
- Add styling where appropriate

### Step 4: Validate Syntax
Before presenting the diagram, verify:
- [ ] All brackets/braces are balanced
- [ ] Node IDs are unique and valid
- [ ] All connections reference existing nodes
- [ ] Labels are properly quoted if they contain special characters
- [ ] Syntax matches the chosen diagram type
- [ ] No trailing spaces or invalid characters

### Step 5: Present the Diagram
- Show the complete Mermaid code block
- Add a brief description of what it represents
- Mention any design decisions or alternatives considered

## Validation Checklist

Before finalizing ANY diagram:

1. **Syntax Correctness**
   - [ ] No unclosed brackets, braces, or quotes
   - [ ] Valid operators for diagram type
   - [ ] Proper indentation and formatting

2. **Node Definitions**
   - [ ] All nodes are defined before being referenced
   - [ ] Node IDs follow naming conventions (alphanumeric, -, _)
   - [ ] Labels are clear and descriptive

3. **Connections**
   - [ ] All connections use valid syntax
   - [ ] All referenced nodes exist
   - [ ] Arrow directions are correct

4. **Readability**
   - [ ] Not overcrowded (consider splitting if >20 nodes)
   - [ ] Logical flow (usually top-to-bottom or left-to-right)
   - [ ] Consistent styling

5. **Purpose**
   - [ ] Diagram serves its intended purpose
   - [ ] Appropriate detail level
   - [ ] Clear and unambiguous

## Best Practices

1. **Use meaningful IDs**: Instead of `node1`, use `userAuth` or `dbQuery`
2. **Keep labels concise**: Use short, clear descriptions
3. **Maintain consistent styling**: Use similar formatting throughout
4. **Group related elements**: Organize logically related components
5. **Add context**: Include title or description above the diagram
6. **Test before finalizing**: Mentally render or use Mermaid Live Editor

## Common Syntax Patterns

### Flowchart Node Types
```
id[Rectangle]
id([Rounded])
id[(Database)]
id((Circle))
id{Diamond/Decision}
id{{Hexagon}}
id[/Parallelogram/]
id[\Parallelogram\]
id[/Trapezoid\]
id[\Trapezoid/]
```

### Flowchart Arrows
```
A --> B          Simple arrow
A -->|text| B    Arrow with label
A -.-> B         Dotted arrow
A ==> B          Thick arrow
A <--> B         Bidirectional
```

### Sequence Diagram Messages
```
A->>B: Sync message
A-->>B: Async response
A--)B: Async message
A--xB: Message to destroyed participant
activate/deactivate for lifelines
```

### State Diagram Syntax
```
[*] --> State1
State1 --> State2: transition
State2 --> [*]
```

## Quick Reference: Diagram Type Selection

| Need to Show | Use This Diagram Type |
|--------------|----------------------|
| Process flow, algorithm | Flowchart |
| API interactions, message passing | Sequence Diagram |
| Object relationships, inheritance | Class Diagram |
| State transitions, lifecycle | State Diagram |
| Database schema | ERD |
| Project timeline | Gantt Chart |
| Data distribution | Pie Chart |
| System components | Component Diagram |
| User experience flow | User Journey |

## Examples

For comprehensive examples, see [EXAMPLES.md](EXAMPLES.md).
For advanced validation patterns, see [VALIDATION.md](VALIDATION.md).

## Common Issues and Solutions

| Issue | Solution |
|-------|----------|
| Diagram not rendering | Check for unclosed brackets or quotes |
| Connection errors | Verify node IDs match exactly (case-sensitive) |
| Syntax errors | Validate against diagram type syntax |
| Overcrowded diagram | Break into multiple diagrams or increase detail level |
| Unclear visualization | Add descriptive labels or choose different diagram type |

## When to Use This Skill

Invoke this skill when you need to:
- Document system architecture
- Visualize workflow or process
- Show data relationships
- Illustrate API interactions
- Create project timelines
- Display decision trees
- Show state transitions
- Map user journeys
- Explain complex concepts visually

## Output Format

Always provide:
1. Brief description of what the diagram shows
2. The complete Mermaid code block
3. Confirmation of syntax validation
4. Optional: Alternative approaches or improvements

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/testaco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
