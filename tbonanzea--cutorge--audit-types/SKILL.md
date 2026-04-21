---
name: audit-types
description: Audit TypeScript type safety and patterns Use when this capability is needed.
metadata:
  author: tbonanzea
---

# TypeScript Audit

Audit type safety across the codebase.

## Scan Areas

1. **Zod Usage**
   - Find Zod schemas
   - Verify ONLY at boundaries (API routes, forms, env)
   - Flag internal Zod validation

2. **Type Assertions**
   - Find all 'as' type assertions
   - Verify each has validation
   - Flag unsafe casts

3. **Any Types**
   - Find all 'any' usage
   - Verify justification for each
   - Suggest proper types

4. **Type Inference**
   - Find duplicate type definitions
   - Verify z.infer usage for schemas
   - Check Prisma type usage

5. **Generics**
   - Verify constraints on generics
   - Check for unconstrained <T>
   - Ensure type safety

6. **Nullability**
   - Check optional chaining usage
   - Verify non-null assertions (!)
   - Ensure undefined handled

## Output

**CRITICAL**: Type unsafety risks
**WARNING**: Suboptimal patterns
**SUGGESTION**: Better inference

Reference: supabase-postgres-best-practices (for Prisma types)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tbonanzea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
