---
name: godot-doc-search
description: | Use when this capability is needed.
metadata:
  author: minami110
---

# GDScript API Lookup

Research Godot/GDScript implementations using Deepwiki (architecture) and Context7 (API docs).

## Workflow

### Step 1: Deepwiki ask_question (FIRST)

Ask about implementation approach:

```
mcp__deepwiki__ask_question
  repoName: "godotengine/godot"
  question: "How to implement [feature]? What nodes and signals should be used?"
```

Returns:
- Recommended nodes and scene structure
- Signals and methods to use
- Implementation steps

### Step 2: Context7 (Code Examples)

Get code examples using keywords from Step 1:

```
mcp__context7__query-docs
  libraryId: "/websites/godotengine_en"
  query: "[keywords from Deepwiki response]"
```

Note: Use `/websites/godotengine_en` for latest documentation (83,702+ snippets).

## Example Queries

| Task | Deepwiki Question | Context7 Topic |
|------|-------------------|----------------|
| Damage zone | "How to implement a hazard that damages player on collision in 2D?" | "Area2D body_entered signal" |
| Pickup item | "How to implement collectible items in 2D?" | "Area2D collision pickup" |
| Moving platform | "How to implement moving platforms in 2D platformer?" | "AnimatableBody2D platform" |
| Enemy AI | "How to implement basic enemy AI with patrol and chase behavior?" | "NavigationAgent2D pathfinding" |
| Save system | "How to implement save/load game state?" | "ConfigFile save load" |

## Sources

- **Deepwiki**: Godot Engine source code insights (godotengine/godot)
- **Context7**: Official Godot documentation (/websites/godotengine_en)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/minami110) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
