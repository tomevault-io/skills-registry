---
name: frontend-code-review
description: Senior-level code review for React 19.2 + Next.js 16 pull requests applying Google, Facebook, and enterprise engineering standards. Use when reviewing frontend PRs, providing code review feedback, checking React/Next.js code quality, or when users request code review for TypeScript, React, or Next.js projects. Ensures production-grade quality, performance, accessibility, and maintainability. Use when this capability is needed.
metadata:
  author: yoriichi-dang
---

# Frontend Code Review

Production-grade code review standards for React 19.2 + Next.js 16 applications.

## Review Workflow

Follow this comprehensive process for high-quality code reviews:

### Phase 1: Understand Codebase Context (5-10 min)

**CRITICAL: Always analyze the codebase BEFORE reviewing PR changes.**

This ensures reviews are consistent with existing patterns and technical decisions.

#### Step 1: Identify Project Structure
```bash
# View project root to understand architecture
view /path/to/project

# Common paths to check:
# - src/ or app/ - main application code
# - package.json - dependencies and scripts
# - tsconfig.json - TypeScript configuration
# - next.config.js - Next.js configuration
# - .eslintrc / prettier config - code style rules
```

#### Step 2: Analyze Dependencies & Tech Stack
```bash
# Read package.json to understand:
view package.json

# Key things to note:
✓ React version (should be 19.2+)
✓ Next.js version (should be 16+)
✓ State management (React Query, Zustand, Redux?)
✓ Form handling (react-hook-form, Formik?)
✓ Styling (Tailwind, CSS Modules, styled-components?)
✓ Testing (Jest, Vitest, Playwright?)
✓ Validation (Zod, Yup?)
✓ UI components (shadcn/ui, MUI, custom?)
```

#### Step 3: Review Project Configuration
```bash
# Check TypeScript config
view tsconfig.json
# Note: strict mode? paths configured?

# Check Next.js config  
view next.config.js
# Note: experimental features? image domains?

# Check ESLint rules
view .eslintrc.js
# Note: custom rules? extends what configs?
```

#### Step 4: Study Existing Code Patterns
```bash
# Look at similar files to understand patterns
view src/features/[similar-feature]
view src/components/[similar-component]

# Key patterns to identify:
✓ Component structure (FC vs function, props destructuring?)
✓ File organization (co-located tests? index exports?)
✓ Naming conventions (PascalCase, camelCase, kebab-case?)
✓ Import style (absolute paths? aliases?)
✓ Error handling patterns (try-catch? ErrorBoundary?)
✓ API call patterns (fetch? React Query? Server Actions?)
✓ Type definitions (interface? type? where defined?)
```

#### Step 5: Check Project Standards
```bash
# Look for documentation
view README.md
view CONTRIBUTING.md
view docs/CODING_STANDARDS.md

# Check for custom conventions
view .github/pull_request_template.md
```

**After context analysis, document key findings:**
- Tech stack being used
- Code patterns followed
- Project-specific conventions
- Any unusual configurations

### Phase 2: Review PR Changes (10-15 min)

Now review the actual PR with full context.

#### Step 1: Pre-Review Checks (2 min)
```
✓ PR description explains WHY (not just WHAT)
✓ Size <400 LOC (request split if larger)
✓ CI passes (tests, linting, build)
✓ Screenshots/videos for UI changes
```

#### Step 2: Architecture Review (5 min)
```
✓ Consistent with project architecture?
✓ Follows existing patterns?
✓ RSC-first? ('use client' only when needed)
✓ Server/client split logical?
✓ Feature properly encapsulated?
✓ loading.tsx + error.tsx present?
✓ Uses project's chosen libraries (not introducing new ones unnecessarily)
```

#### Step 3: Code Quality Review (10 min)
```
✓ Matches project TypeScript strictness?
✓ Follows project naming conventions?
✓ Import style consistent?
✓ Uses project's validation approach (Zod/Yup)?
✓ Uses project's state management (React Query/Zustand)?
✓ Anti-patterns avoided?
✓ Tests match project test patterns?
✓ Bundle impact acceptable? (<200KB/route)
```

#### Step 4: UX/A11y Review (5 min)
```
✓ Keyboard accessible? (Tab, Enter, Escape)
✓ Semantic HTML? (not div-soup)
✓ ARIA only when necessary?
✓ Color contrast ≥4.5:1?
✓ Matches project's UI component library usage?
```

### Phase 3: Provide Context-Aware Feedback

Reference the codebase patterns in your feedback:

**Good Example:**
```
🟡 MAJOR: Inconsistent with project patterns

This uses fetch() directly, but the project uses React Query everywhere 
else (see src/features/users/api/useUsers.ts for example).

For consistency:
export function useProducts() {
  return useQuery({
    queryKey: ['products'],
    queryFn: () => api.products.list(),
  });
}
```

## Priority Levels

Use these tags in review comments:

- **🔴 BLOCKER** - Must fix before merge (security, critical bugs, data loss)
- **🟡 MAJOR** - Should fix (performance, accessibility, maintainability)
- **🔵 MINOR** - Nice to have (style, readability, better patterns)
- **⚪ OPTIONAL** - Personal preference (formatting, syntax choices)

## Codebase Context Analysis

**Load `references/codebase-analysis.md` for:**
- Detailed guide on what to look for in each config file
- Checklist for identifying project patterns
- How to analyze existing code for conventions
- Examples of context-aware review comments

## Critical Checks

**Load `references/critical-checks.md` for:**
- Complete security checklists
- React fundamentals validation
- Performance and accessibility standards

## Common Anti-Patterns

Load `references/anti-patterns.md` for examples of state management issues, missing cleanup, unnecessary client components, and key problems.

## Review Comment Templates

Load `references/comment-templates.md` for examples of constructive feedback at each priority level with suggested fixes.

## PR Type-Specific Checks

For each PR type, use these focused checklists:

### UI Component PR
- Component <200 lines (split if larger)
- Props typed (TypeScript interface)
- Responsive design tested (mobile, tablet, desktop)
- Dark mode support (if theme enabled)
- Visual regression test (Playwright screenshot)

### API Integration PR
- React Query used for server state
- Loading states handled (Suspense boundaries)
- Error states handled (error.tsx or ErrorBoundary)
- Optimistic updates (useOptimistic if needed)
- Query keys use factory pattern
- MSW handlers for tests

### Form PR
- react-hook-form + Zod validation
- Server Actions for mutations
- useActionState for loading/error states
- Accessible labels (not placeholders)
- Error messages clear and helpful
- Success feedback provided

### Performance Optimization PR
- Before/after metrics documented
- React Profiler used to measure
- Bundle size analyzed
- Core Web Vitals improved

## Red Flags

Stop and request discussion if you see:

⛔ 'any' types (except justified edge cases)
⛔ Commented-out code blocks
⛔ console.log left in production code
⛔ TODO comments without issue links
⛔ Deep feature imports (bypass public API)
⛔ Missing loading/error states
⛔ Large files (>300 lines)
⛔ God components (too many responsibilities)
⛔ Duplicate code (3+ occurrences)
⛔ Hardcoded values (API URLs, magic numbers)

## Giving Feedback

### ✅ DO
- Be specific: "Line 45: Missing null check for user.profile"
- Explain why: "...could cause runtime error on logout"
- Suggest solution: "Add optional chaining: user.profile?.name"
- Ask questions: "What's the reason for this approach?"
- Praise good work: "Nice use of useTransition here!"

### ❌ DON'T
- Be vague: "This looks wrong"
- Just criticize: "Bad code"
- Make demands: "Change this now"
- Nitpick style: "I prefer single quotes" (use linters)
- Block on personal preference

## When to Approve

**Approve when:**
- All 🔴 BLOCKERS resolved
- Most 🟡 MAJOR issues addressed
- Architecture is sound
- Tests pass and provide value
- Won't cause production issues

**Don't block on:**
- Minor style preferences
- Perfect code (good enough > perfect)
- Nice-to-have improvements
- Theoretical future scenarios

## Key Standards

```typescript
// React 19.2 Patterns
✓ useEffectEvent for stable callbacks
✓ useActionState for form handling
✓ useOptimistic for UI updates
✓ use() for promises/context

// Next.js 16 Features
✓ 'use cache' for function caching
✓ cacheLife() for cache duration
✓ cacheTag() for invalidation
✓ Proxy for auth/rewrites

// Type Safety
✓ Strict TypeScript (no 'any')
✓ Zod at boundaries (API, forms)
✓ DTO → ViewModel mapping

// Performance Budget
✓ LCP <2.5s
✓ FID <100ms
✓ CLS <0.1
✓ Bundle <200KB/route

// Accessibility
✓ WCAG 2.1 AA
✓ Semantic HTML
✓ Keyboard nav
✓ 4.5:1 contrast
```

## Philosophy

> "Code reviews are about building better engineers, not just better code."

- **Speed matters:** Slow reviews kill momentum (respond <24h)
- **Be kind:** Assume best intentions
- **Teach, don't tell:** Explain reasoning
- **Celebrate wins:** Call out great work
- **Stay humble:** You might be wrong too

Your goal is to ship quality code while growing the team. When in doubt, approve and follow up later rather than blocking progress on minor issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yoriichi-dang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
