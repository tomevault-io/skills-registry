---
name: laws-of-ux
description: > Use when this capability is needed.
metadata:
  author: 333-333-333
---

## When to Use

- Designing new features or flows
- Reviewing UI/UX for improvements
- Justifying design decisions to stakeholders
- Optimizing existing interfaces

## Core Laws

### Interaction & Time

| Law | Principle | Flutter Application |
|-----|-----------|---------------------|
| **Fitts's Law** | Time to target = f(distance, size) | Large touch targets (48dp+), important actions within thumb reach |
| **Hick's Law** | Decision time ↑ with more choices | Limit options, use progressive disclosure |
| **Doherty Threshold** | Response < 400ms keeps flow | Show loading states, optimistic UI updates |

### Memory & Cognition

| Law | Principle | Flutter Application |
|-----|-----------|---------------------|
| **Miller's Law** | Working memory: 7±2 items | Chunk content, limit nav items to 5-7 |
| **Cognitive Load** | Minimize mental effort | Simple layouts, clear hierarchy, familiar patterns |
| **Chunking** | Group related info | Use cards, sections, visual grouping |

### Perception & Gestalt

| Law | Principle | Flutter Application |
|-----|-----------|---------------------|
| **Law of Proximity** | Near = related | Group related controls, consistent spacing |
| **Law of Similarity** | Similar = related | Consistent button styles, icon families |
| **Law of Common Region** | Shared boundary = group | Cards, outlined sections, backgrounds |
| **Law of Prägnanz** | Simplest interpretation wins | Clean shapes, minimal decoration |

### Behavior & Psychology

| Law | Principle | Flutter Application |
|-----|-----------|---------------------|
| **Jakob's Law** | Users expect familiar patterns | Follow platform conventions, standard nav |
| **Peak-End Rule** | Judge by peak + end moments | Strong onboarding, satisfying completion |
| **Von Restorff Effect** | Different = memorable | Highlight CTAs, use contrast for key actions |
| **Serial Position Effect** | Remember first/last best | Important items at top/bottom of lists |
| **Goal-Gradient Effect** | Motivation ↑ near goal | Progress indicators, completion percentages |
| **Zeigarnik Effect** | Incomplete = memorable | Progress saving, resume prompts |

### Complexity & Design

| Law | Principle | Flutter Application |
|-----|-----------|---------------------|
| **Tesler's Law** | Complexity can't be eliminated, only moved | Hide complexity from users, smart defaults |
| **Occam's Razor** | Simplest solution is best | Remove unnecessary elements, clear paths |
| **Aesthetic-Usability** | Pretty = perceived as usable | Polish visual design, attention to detail |
| **Postel's Law** | Accept liberally, send conservatively | Flexible inputs, strict validation output |

## Decision Framework

```
User struggling with choices?     → Hick's Law (reduce options)
UI feels slow?                    → Doherty Threshold (< 400ms feedback)
Users missing key actions?        → Fitts's Law (bigger targets)
                                  → Von Restorff (make it stand out)
Users confused by layout?         → Gestalt laws (proximity, similarity)
Users abandoning flows?           → Peak-End Rule (improve completion)
                                  → Goal-Gradient (show progress)
Users not finding features?       → Jakob's Law (use familiar patterns)
Too much on screen?               → Miller's Law (chunk to 7±2)
                                  → Cognitive Load (simplify)
```

## Quick Reference

> See [assets/laws_quick_reference.md](assets/laws_quick_reference.md) for a printable checklist.

## Anti-Patterns

| ❌ Violation | Law Broken | ✅ Fix |
|-------------|------------|--------|
| 15 nav items | Miller's Law | Group into 5 categories |
| Tiny 24px buttons | Fitts's Law | Minimum 48dp touch target |
| 500ms+ response, no feedback | Doherty Threshold | Immediate loading indicator |
| All buttons look the same | Von Restorff | Highlight primary CTA |
| No progress in long forms | Goal-Gradient | Add progress indicator |
| Custom unfamiliar navigation | Jakob's Law | Use standard patterns |
| Dense wall of text | Chunking | Break into scannable sections |

## Resources

- **Source**: [lawsofux.com](https://lawsofux.com)
- **Quick reference**: See [assets/laws_quick_reference.md](assets/laws_quick_reference.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/333-333-333) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
