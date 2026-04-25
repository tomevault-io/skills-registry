---
name: spec
description: Generate complete story specification by running all spec skills in sequence (story, mockups, api-spec, test-spec). Use when user wants full documentation for a story. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Generate Complete Story Specification

Automatically execute ALL FOUR sub-skills in sequence without stopping or asking for confirmation.

## Usage
```
/spec "Story name"
/spec 5                    # By MVP story number
/spec                      # Interactive selection
```

## Workflow

1. **Parse story** from user input (name, number, or interactive)
2. **Check** for `ProductSpecification/stories/NN-story-name/story-specifics.txt` (additional context)
3. **Execute all four skills sequentially:**
   - `Skill(skill="story", args="<story-name>")`
   - `Skill(skill="mockups", args="<story-name>")`
   - `Skill(skill="api-spec", args="<story-name>")`
   - `Skill(skill="test-spec", args="<story-name>")`
4. **Provide summary** of all generated files

Story mapping: see `.claude/shared/story-mapping.md`

## Rules

- Execute ALL FOUR skills automatically — do NOT stop between them
- Each skill must complete before starting the next
- If any skill fails, report error but continue tracking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
