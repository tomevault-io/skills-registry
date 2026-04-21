---
name: prisma-data-modeling
description: Guidelines for designing Prisma schema, relationships, and indexes. Use when modifying schema.prisma or discussing database structure. Use when this capability is needed.
metadata:
  author: frknkoseoglu
---

# Prisma Data Modeling Skill

This skill ensures the database scales well and maintains data integrity for the Midas Web Concept.

## 🏗️ Modeling Standards

### 1. Naming Conventions
- **Models:** PascalCase (e.g., `LimitOrder`, `PortfolioItem`).
- **Fields:** camelCase (e.g., `createdAt`, `userId`).
- **Foreign Keys:** Use `userId` pointing to `User`.

### 2. Monetary Fields
ALWAYS use the following definition for price/balance fields:
```prisma
amount Decimal @db.Decimal(19, 4)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/frknkoseoglu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
