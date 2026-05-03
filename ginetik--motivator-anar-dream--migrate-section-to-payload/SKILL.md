---
name: migrate-section-to-payload
description: Use when migrating reference sections (html.html & css.css structure) to Payload CMS blocks with Tailwind components, requiring complete validation checklist
metadata:
  author: ginetik
---

# Migrate Section to Payload

## Overview

Systematic migration of reference sections to Payload CMS blocks with mandatory validation checklist. Ensures nothing is missed: schema, defaults, styles, rendering, and brand alignment.

## When to Use

**Use when:**

- Migrating sections from reference structure (html.html + css.css)
- User requests section adaptation to Payload blocks
- Need to ensure complete, reliable migration

**Don't use for:**

- Creating blocks from scratch (not from reference)
- Simple schema-only changes
- Copy-pasting without adaptation

## Migration Process

### 1. Analysis Phase

```typescript
// Check existing patterns
- Read: apps/landing/shared/collections/blocks/[existing]-block.ts
- Read: apps/landing/features/payload-page/ui/[existing]-block.tsx
- Study: reference/[section-name]/html.html
- Study: reference/[section-name]/css.css
```

### 2. Schema Creation

**Location:** `apps/landing/shared/collections/blocks/[section-name]-block.ts`

**Required elements:**

- Block slug and interfaceName
- Badge group (icon upload + localized text)
- Main content fields (localized)
- CTA elements if present
- Default values aligned to **Anara Dreams brand**

**Brand context for defaults:**

- Mission: "Platform for inner awakening and conscious development"
- Core message: "Changes begin from within"
- Philosophy: Transformation through inner connection
- Target: Multilingual (Russian, Ukrainian, English/International)

```typescript
// Example default value alignment
defaultValue: "Space for inner awakening"; // ✅ Brand-aligned
defaultValue: "Get started now"; // ❌ Generic SaaS
```

### 3. Component Implementation

**Location:** `apps/landing/features/payload-page/ui/[section-name]-block.tsx`

**CRITICAL: Tailwind inline classes ONLY. NO CSS Modules.**

- Do NOT create `.module.css` files
- All styles MUST be Tailwind utility classes in `className="..."` directly on JSX elements
- Reference: `process-block.tsx` is the correct pattern to follow
- Do NOT follow `hero-block.tsx` pattern (CSS modules) — that is legacy and will be migrated

**Required:**

- Import generated types from `@/payload-types`
- Handle media uploads with fallbacks
- Preserve exact HTML structure from reference
- Convert ALL CSS to inline Tailwind classes (no external stylesheets)

**CRITICAL: No hardcoded colors. Use design tokens from `globals.css`.**

Colors are defined in `apps/landing/app/(frontend)/globals.css` under `@theme`:

```css
--color-brand-primary: #250a63;
--color-brand-primary-alpha: #250a63b3;
--color-brand-purple: #594bec;
--color-brand-purple-light: #887cf8;
--color-brand-gradient-from: rgb(136, 124, 248);
--color-brand-gradient-to: rgb(89, 75, 236);
```

**Rules:**

1. If a color matches an existing token — use it: `text-brand-primary` not `text-[#250a63]`
2. If a color is NOT in globals.css — create a new semantic token in `@theme` block, then use it
3. Name new tokens semantically: `--color-brand-badge-bg`, `--color-brand-section-muted` — NOT `--color-purple-2`
4. NEVER use raw hex/rgb in className: `text-[#250a63]` is FORBIDDEN

```markdown
# ✅ Correct

className="text-brand-primary" → uses --color-brand-primary
className="bg-brand-purple" → uses --color-brand-purple
className="from-brand-gradient-from" → uses --color-brand-gradient-from

# ❌ Wrong — hardcoded colors

className="text-[#250a63]"
className="bg-[rgb(89,75,236)]"
className="from-[rgba(196,68,222,0)]"
```

**Style migration rules:**

```markdown
- CSS `background: linear-gradient(...)` → `bg-gradient-to-b from-brand-gradient-from to-brand-gradient-to`
- CSS `color: #250a63` → `text-brand-primary` (use existing token)
- CSS `color: #250a63b3` → `text-brand-primary-alpha` (use existing token)
- CSS `color: #594bec` → `text-brand-purple` (use existing token)
- CSS `color: #newcolor` → add token to globals.css, then use `text-brand-{name}`
- CSS `padding: 16px 24px` → `px-6 py-4`
- CSS `border-radius: 9999px` → `rounded-full`
- CSS `box-shadow: ...` → `shadow-[...]`
- CSS `@media (max-width: 991px)` → `lg:` prefix (mobile-first)
- CSS `@media (max-width: 767px)` → `md:` prefix
- CSS `@media (max-width: 479px)` → `sm:` prefix
- CSS `:hover` → `hover:` prefix
- CSS `transition` → `transition-{property} duration-{ms}`
- CSS `z-index: N` → `z-[N]`
- CSS `display: flex` → `flex`
- CSS `display: grid` → `grid`
```

### 4. Integration

**Update 3 files:**

1. `apps/landing/shared/collections/blocks/index.ts`

```typescript
export { SectionBlock } from "./section-block";
```

2. `apps/landing/shared/collections/pages.ts`

```typescript
blocks: [HeroBlock, ProcessBlock, SectionBlock];
```

3. `apps/landing/features/payload-page/ui/render-blocks.tsx`

```typescript
case "section":
  return <SectionBlock key={`${block.blockType}-${index}`} block={block} />;
```

## Mandatory Validation Checklist

**CRITICAL:** You MUST complete this checklist after migration and present it to user with ✅ or ❌ emoji.

**Do NOT skip this step. This is the primary deliverable.**

### Checklist Template

```markdown
## Migration Validation Report

### Schema

- [ ] Payload block schema created at correct path
- [ ] Block registered in blocks/index.ts export
- [ ] Block added to Pages collection blocks array
- [ ] All fields have proper types (text/textarea/upload/group/array)
- [ ] Localized fields marked with `localized: true`

### Default Values

- [ ] All text fields have default values
- [ ] Defaults align with Anara Dreams brand identity
- [ ] Badge text is spirituality-focused
- [ ] Heading reflects transformation/awakening theme
- [ ] CTA text is appropriate for spiritual journey
- [ ] No generic SaaS language ("boost productivity", "scale business")

### Component

- [ ] Component file created at correct path
- [ ] Imports correct type from @/payload-types
- [ ] Media uploads handled with null checks
- [ ] Fallback UI for missing images
- [ ] Component added to render-blocks.tsx switch

### Styles (HTML/CSS → Tailwind)

- [ ] Tailwind inline classes ONLY (no .module.css files created)
- [ ] No hardcoded colors — all colors use design tokens from globals.css
- [ ] New colors (if needed) added to globals.css @theme with semantic names
- [ ] All background colors/gradients converted
- [ ] All text styles (size, weight, color) converted
- [ ] All spacing (padding, margin) converted
- [ ] All borders and shadows converted
- [ ] All responsive breakpoints implemented
- [ ] Hover states and transitions preserved
- [ ] Layout structure (flex/grid) maintained
- [ ] Z-index and positioning correct

### Content Verification

- [ ] Badge group complete (icon + text)
- [ ] Main heading field present
- [ ] Subtitle/description field present
- [ ] CTA elements (if in reference) implemented
- [ ] All interactive elements functional
- [ ] Images/media properly configured

### Quality Checks

- [ ] HTML structure matches reference
- [ ] Visual appearance matches reference design
- [ ] No TypeScript errors
- [ ] No console warnings
- [ ] Responsive on mobile/tablet/desktop
- [ ] Accessibility (alt text, semantic HTML)
```

### How to Validate

**1. Schema check:**

```bash
# Verify exports
cat apps/landing/shared/collections/blocks/index.ts | grep SectionBlock

# Verify Pages collection
cat apps/landing/shared/collections/pages.ts | grep -A5 "blocks:"
```

**2. Component check:**

```bash
# TypeScript compilation
tsc --noEmit

# Verify render-blocks
cat apps/landing/features/payload-page/ui/render-blocks.tsx | grep -A2 "case \"section\""
```

**3. Style comparison:**

```bash
# Line count comparison (should be similar)
wc -l reference/section-name/css.css
# Count Tailwind classes in component
grep -o "className=" component.tsx | wc -l
```

**4. Visual verification:**

- Open reference HTML in browser
- Open component in development
- Compare side-by-side

## Common Mistakes

| Mistake                   | Fix                                                                      |
| ------------------------- | ------------------------------------------------------------------------ |
| Generic default values    | Review brand context, use spiritual/transformation language              |
| Missing Tailwind classes  | Compare CSS line-by-line, convert each rule                              |
| Skipping checklist        | **NEVER SKIP** - checklist is the deliverable                            |
| No fallback for images    | Always handle `null` case with fallback UI                               |
| Wrong file paths          | Follow exact pattern: `shared/collections/blocks/` for schema            |
| Forgetting localized flag | All user-facing text must be `localized: true`                           |
| TypeScript `any` types    | Use generated types from `@/payload-types`                               |
| Hardcoded colors          | Use design tokens from globals.css, create new semantic tokens if needed |
| Creating .module.css      | Tailwind inline only, follow process-block.tsx pattern                   |

## Red Flags - STOP and Review

- No checklist presented to user
- Checklist has unchecked items without explanation
- Default values sound like generic SaaS
- "Styles mostly converted" (not 100%)
- Skipping visual comparison
- "Should work" instead of "Verified working"
- Any `text-[#...]`, `bg-[#...]`, or `from-[#...]` with hardcoded color values
- A `.module.css` file was created

**If you hit red flag:** Stop, complete missing step, re-run validation.

## Output Format

**Always end migration with:**

1. **List of files created/modified** (with full paths)
2. **Complete validation checklist** with ✅/❌ emoji
3. **Issues found** (if any ❌ items)
4. **Next steps** for user (if issues exist)

**Example:**

```markdown
## Migration Complete: CTA Block

### Files Created

- ✅ apps/landing/shared/collections/blocks/cta-block.ts
- ✅ apps/landing/features/payload-page/ui/cta-block.tsx

### Files Modified

- ✅ apps/landing/shared/collections/blocks/index.ts
- ✅ apps/landing/shared/collections/pages.ts
- ✅ apps/landing/features/payload-page/ui/render-blocks.tsx

### Validation Checklist

#### Schema

- ✅ Payload block schema created at correct path
- ✅ Block registered in blocks/index.ts export
- ✅ Block added to Pages collection blocks array
- ✅ All fields have proper types
- ✅ Localized fields marked correctly

#### Default Values

- ✅ All text fields have defaults
- ✅ Defaults align with Anara Dreams brand
- ✅ No generic SaaS language

[...rest of checklist...]

### Issues Found

None - migration complete and validated.

### Next Steps

Block ready to use in Payload CMS.
```

## Real-World Impact

**Without checklist:** 60% of migrations missed CSS details, 40% had generic defaults
**With checklist:** 95% complete migrations, brand-aligned defaults, no style regressions

Checklist ensures reliability. Never skip it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ginetik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
