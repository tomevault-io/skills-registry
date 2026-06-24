---
name: phase-1-schema
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Phase 1: Schema/Terminology Definition

> Define the language and data structures of your project

## Purpose

Create a shared vocabulary and data model before writing any code.

## Deliverables

### 1. Terminology Glossary

```markdown
## Terms

| Term | Definition | Example |
|------|------------|---------|
| User | A registered account | john@email.com |
| Order | A purchase transaction | ORD-123 |
| Product | An item for sale | Blue Widget |
```

### 2. Entity Definitions

```typescript
interface User {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
}

interface Order {
  id: string;
  userId: string;
  items: OrderItem[];
  total: number;
  status: 'pending' | 'paid' | 'shipped';
}
```

### 3. Relationship Diagram

```
User (1) ──── (N) Order (N) ──── (N) Product
              │
              └── (N) OrderItem
```

## Output

Save to: `docs/01-plan/schema.md`

## Next Phase

After completion: `/phase-2-convention` or `/pdca design {feature}`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
