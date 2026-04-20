---
name: character-arc
description: Track and analyze character development arcs throughout the manuscript Use when this capability is needed.
metadata:
  author: mikkelkrogsholm
---

# Character Arc Tracking Skill

Analyze character development and track character arcs across the narrative.

## Usage

```bash
/character-arc --character anna
/character-arc --all
```

## Features

- Track character appearances across sections
- Analyze character development progression
- Identify arc milestones (introduction, growth, climax, resolution)
- Detect inconsistencies in character behavior
- Visualize character journey

## Arc Analysis

Analyzes:
- **Introduction**: When and how character is introduced
- **Development**: Changes in traits, relationships, goals
- **Turning Points**: Key moments that transform the character
- **Resolution**: Final state and arc completion

## Query Example

```sql
SELECT
    section.sequence,
    section.content,
    character.description
FROM character:anna<-appears_in<-section
ORDER BY section.sequence
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikkelkrogsholm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
