---
name: documentation-standards
description: Clear technical documentation with JSDoc, READMEs, Mermaid diagrams, ISMS policy references, and comprehensive code examples Use when this capability is needed.
metadata:
  author: hack23
---

# Documentation Standards Skill

## Context
Applies when writing READMEs, documenting APIs/functions/classes, creating architecture docs, adding JSDoc, creating diagrams, documenting security policies, or writing guides.

## Rules

1. **ISMS References**: Security-related code must reference [Hack23 ISMS](https://github.com/Hack23/ISMS-PUBLIC) policies
2. **JSDoc for Public APIs**: All exports must have JSDoc with `@param`, `@returns`, `@example`
3. **Type Information**: Include TypeScript types in JSDoc documentation
4. **Provide Examples**: Working code examples for all public APIs
5. **Document Exceptions**: `@throws` for all possible error conditions
6. **Mermaid Diagrams**: Architecture and flow diagrams in Mermaid, not screenshots
7. **Keep READMEs Current**: Update when adding features or changing setup
8. **Link External Docs**: Reference official docs for frameworks and standards
9. **Show Anti-Patterns**: Include incorrect examples to prevent mistakes
10. **Accessible**: Semantic markdown, descriptive links, alt text for images
11. **Heading Hierarchy**: H1 → H2 → H3, never skip levels
12. **Code Block Languages**: Always specify (```typescript, ```bash)

## Examples

### ✅ JSDoc for Game Function

```typescript
/**
 * Calculates the target count based on current level.
 *
 * @param level - Current game level (1+)
 * @returns Number of active targets for the level
 *
 * @example
 * ```typescript
 * getTargetCountForLevel(1); // 1
 * getTargetCountForLevel(5); // 2
 * getTargetCountForLevel(8); // 3
 * ```
 */
export function getTargetCountForLevel(level: number): number {
  if (level <= 3) return 1;
  if (level <= 6) return 2;
  return 3;
}
```

### ✅ Mermaid Architecture Diagram

````markdown
```mermaid
graph TB
  App[App.tsx] --> Canvas[Canvas]
  App --> HUD[HUD]
  App --> Overlay[GameOverlay]
  Canvas --> Scene[GameScene]
  Scene --> Target[TargetSphere]
  Scene --> Particles[BackgroundParticles]
  App --> useGameState[useGameState]
  App --> useAudio[useAudioManager]
```
````

### ❌ Anti-Patterns

```typescript
// BAD: No JSDoc on exported function
export function calc(x: number) { return x * 2; }

// BAD: No @param or @returns
/** Does calculation */ export function calc(x: number) { /* ... */ }

// BAD: Broken/untested example in JSDoc
/**
 * Doubles a number.
 *
 * @example
 * ```typescript
 * calc("2"); // 4
 * ```
 */
export function calc(value: number): number { return value * 2; }
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hack23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
