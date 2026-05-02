---
name: c4-dsl-generator
description: Generate Structurizr DSL files (.dsl) for C4 architecture diagrams. Use when users need to create, modify, or review C4 model diagrams including the four C4 levels — System Context (L1), Container (L2), Component (L3), and Code (L4). Triggers on requests like "create a C4 diagram", "generate architecture diagram", "Structurizr DSL", "system context diagram", "container diagram", "component diagram", "code diagram", or any request to visualize software architecture using the C4 model. Use when this capability is needed.
metadata:
  author: anitabee
---

# C4 DSL Generator

Generate valid Structurizr DSL files for C4 architecture diagrams. The C4 model defines four levels of abstraction for visualizing software architecture.

## Workflow

1. Determine diagram level(s) needed
2. Gather system context from user (actors, systems, containers, components, code elements)
3. Build the model (elements + relationships)
4. Define views and styles
5. Output a complete `.dsl` file

## The Four C4 Diagram Levels

**Level 1 — System Context**: "Who uses the system and what does it depend on?"
Shows the system in scope, its users, and external dependencies. Start here.

**Level 2 — Container**: "What are the major technical building blocks?"
Shows the high-level technology choices — applications, data stores, message queues, etc.

**Level 3 — Component**: "What are the internal components of a container?"
Shows the structural building blocks inside a container (controllers, services, repositories).

**Level 4 — Code**: "What are the classes/interfaces that make up a component?"
Shows the lowest-level detail — classes, interfaces, and their relationships. Typically auto-generated from source code (UML class diagrams), but can be authored manually for key components.

Each level zooms in deeper. Not every level is needed — L1 and L2 are most common. L4 is optional and often generated from code.

## Core DSL Structure

Every Structurizr DSL file follows this skeleton:

```
workspace "Name" "Description" {
    model {
        // 1. People and software systems
        // 2. Relationships
    }
    views {
        // 3. Diagram definitions
        styles {
            // 4. Visual styling
        }
    }
}
```

## Model Elements

### People and Systems (L1)

```
person <identifier> "Name" "Description" "Tags"
softwareSystem <identifier> "Name" "Description" "Tags"
```

### Containers (L2) — nested inside softwareSystem

```
softwareSystem <identifier> "Name" "Description" {
    container <identifier> "Name" "Description" "Technology" "Tags"
}
```

### Components (L3) — nested inside container

```
container <identifier> "Name" "Description" "Technology" {
    component <identifier> "Name" "Description" "Technology" "Tags"
}
```

### Code Elements (L4)

Structurizr DSL does not have a built-in `code` view type. For Level 4 Code diagrams:

- Use component-level detail with fine-grained components representing classes/interfaces
- Or generate UML class diagrams from source code using IDE tools (IntelliJ, VS Code, PlantUML)
- When authoring manually in DSL, model classes as components with technology annotations like "Java Class", "Interface", "Abstract Class"

### Relationships

```
<source_identifier> -> <dest_identifier> "Description" "Technology" "Tags"
```

## Views

```
systemContext <softwareSystem_id> "key" "Description" {
    include *
    autoLayout lr
}

container <softwareSystem_id> "key" "Description" {
    include *
    autoLayout lr
}

component <container_id> "key" "Description" {
    include *
    autoLayout lr
}
```

### Auto Layout

`autoLayout` accepts: `tb` (top-bottom, default), `bt`, `lr` (left-right), `rl`. Optional parameters: `autoLayout lr 300 150` (rank separation, node separation).

## Default Styles

Always include sensible default styles:

```
styles {
    element "Person" {
        shape Person
        background #08427B
        color #ffffff
    }
    element "Software System" {
        background #1168BD
        color #ffffff
    }
    element "Existing System" {
        background #999999
        color #ffffff
    }
    element "Container" {
        background #438DD5
        color #ffffff
    }
    element "Component" {
        background #85BBF0
        color #000000
    }
}
```

## Quick Example — System Context (L1)

```
workspace "Online Banking" "System Context diagram for Online Banking" {
    model {
        customer = person "Customer" "A bank customer"
        banking = softwareSystem "Online Banking System" "Allows customers to manage accounts" {
            webapp = container "Web Application" "Delivers banking UI" "React"
            api = container "API" "Provides banking functionality" "Java/Spring"
            db = container "Database" "Stores accounts and transactions" "PostgreSQL" "Database"
        }
        mainframe = softwareSystem "Mainframe Banking" "Core banking system" "Existing System"

        customer -> banking "Uses"
        banking -> mainframe "Gets account data from" "SOAP/XML"
    }
    views {
        systemContext banking "SystemContext" "System Context diagram" {
            include *
            autoLayout lr
        }
        styles {
            element "Person" {
                shape Person
                background #08427B
                color #ffffff
            }
            element "Software System" {
                background #1168BD
                color #ffffff
            }
            element "Existing System" {
                background #999999
                color #ffffff
            }
        }
    }
}
```

## References

- **Full DSL syntax**: See [references/dsl-syntax.md](references/dsl-syntax.md) for complete element types, properties, relationship syntax, view options, styles, themes, and groups
- **Complete examples**: See [references/examples.md](references/examples.md) for full working examples of all four C4 levels (System Context, Container, Component, Code)
- **Best practices**: See [references/best-practices.md](references/best-practices.md) for naming conventions, abstraction guidance, common mistakes, and layout tips

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anitabee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
