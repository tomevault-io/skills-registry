---
name: documentation
description: Automated documentation generation and maintenance Use when this capability is needed.
metadata:
  author: qazz92
---

# Documentation Skill

Generate and maintain documentation.

## When to Use
- Documenting new functions
- Updating outdated docs
- Creating API documentation

## What_You_MUST_Do>
1. READ the actual code BEFORE documenting
2. MATCH existing documentation style in project
3. INCLUDE working code examples derived from real code
4. KEEP docs concise - developers skim
5. UPDATE existing docs rather than creating duplicates

## What_You_MUST_NOT_Do>
1. DO NOT document without reading the code first
2. DO NOT invent code examples - derive from real code
3. DO NOT create duplicate documentation
4. DO NOT write long paragraphs - use headings, lists, code blocks
5. DO NOT document stale/incorrect information

## What This Skill Does

### JSDoc Generation
```typescript
/**
 * Calculates the total price of all items.
 *
 * @param items - Array of cart items
 * @returns The sum of all item prices
 */
function calculateTotal(items: CartItem[]): number { }
```

### README Generation
- Installation instructions
- Usage examples
- API reference

## Usage
`/docs-generate <file>` to create documentation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qazz92) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
