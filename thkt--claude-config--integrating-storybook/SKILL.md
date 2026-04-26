---
name: integrating-storybook
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# Storybook Integration

## Core Concepts

| Concept         | Description                                |
| --------------- | ------------------------------------------ |
| Component API   | Props, Variants, States defined in spec.md |
| CSF3            | Component Story Format 3 + autodocs        |
| Auto-generation | `/code` generates Stories from spec.md     |

## Component API Location

Add to spec.md when implementing frontend components.

| Content         | Description                      |
| --------------- | -------------------------------- |
| Props Interface | TypeScript interface             |
| Variants        | Style options                    |
| States          | default, hover, active, disabled |
| Usage Examples  | TSX code                         |

## Workflow

| Command               | Action                                   |
| --------------------- | ---------------------------------------- |
| `/think "Add Button"` | Adds Component API section to spec.md    |
| `/code`               | Generates `Button.stories.tsx` from spec |

## Existing Stories Handling

| Option | Action                    |
| ------ | ------------------------- |
| [O]    | Overwrite existing file   |
| [S]    | Skip - keep existing      |
| [M]    | Merge - show diff, manual |
| [D]    | Diff only - append new    |

## References

| Topic         | File                                                       |
| ------------- | ---------------------------------------------------------- |
| Component API | `${CLAUDE_SKILL_DIR}/references/component-api-template.md` |
| CSF3 Patterns | `${CLAUDE_SKILL_DIR}/references/csf3-patterns.md`          |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
