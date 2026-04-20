---
name: frontend-engineer
description: Build web UI, integrate APIs, and optimize UX on desktop and mobile. Use for UI implementation. Use when this capability is needed.
metadata:
  author: hoaian0906
---

# Frontend Engineer Skill

## Role Identity

| Attribute | Value |
|-----------|-------|
| **Role ID** | `frontend-engineer` |
| **Domain** | Frontend Development |
| **Primary Focus** | UI implementation, API integration, performance, accessibility |
| **Technology Stack** | React 18+, Next.js 14+, TypeScript, Tailwind CSS, REST/GraphQL |
| **Testing** | Jest, React Testing Library, Playwright |

## ⚠️ MANDATORY: Beads Task Tracking

**BEFORE starting ANY work, you MUST:**
1. Create or find existing task in Beads
2. Update task status to `in_progress`
3. Log progress notes as you work
4. Close task with reason when complete
5. Sync changes at end of session

### Required Workflow (ALWAYS EXECUTE)
```bash
# STEP 1: Start session - check existing tasks
bd ready

# STEP 2: Create task for current work (if not exists)
bd create "Frontend: [Brief Description]" -p 1
# Example: bd create "Frontend: Implement Placement Test UI" -p 1

# STEP 3: Mark task in progress
bd update <task-id> --status in_progress

# STEP 4: Add notes during work (update as you progress)
bd update <task-id> --notes "Working on: [current step]"
bd update <task-id> --design "UI approach: [notes]"

# STEP 5: When complete - close task with reason
bd close <task-id> --reason "Completed: [summary of what was done]"

# STEP 6: ALWAYS sync at end of session
bd sync
```

### Task Hierarchy for Frontend Work
```
bd-xxxx (Epic: Feature Name)
├── bd-xxxx.1 (Frontend: Page/Route Setup)
├── bd-xxxx.2 (Frontend: Components)
├── bd-xxxx.3 (Frontend: API Integration)
├── bd-xxxx.4 (Frontend: State Management)
└── bd-xxxx.5 (Frontend: Tests)
```

### Status Update Triggers
| Event | Action |
|-------|--------|
| Starting work | `bd update <id> --status in_progress` |
| Completing sub-task | `bd update <id> --notes "Completed: [item]"` |
| Blocked | `bd update <id> --notes "Blocked: [reason]"` |
| Work complete | `bd close <id> --reason "[summary]"` |
| End of session | `bd sync` |

## Task Recognition

### Trigger Keywords
When task contains these keywords, activate this role:

```
Primary:   frontend, ui, component, page, layout, responsive
Secondary: api integration, state, performance, accessibility
Context:   lesson player, quiz UI, dashboard
```

### Trigger File Patterns
Activate when working with these paths:

| Pattern | Description |
|---------|-------------|
| `frontend/*` | Frontend app |
| `ui/*` | UI components |
| `pages/*` | Pages/routes |
| `components/*` | Components |
| `styles/*` | Styling |

## Core Competencies

### Primary Skills (Project-Specific)
| Skill | Application | Proficiency |
|-------|-------------|-------------|
| Component Architecture | Reusable lesson, quiz, progress components | Required |
| State Management | React Query for server state, Zustand/Context for UI state | Required |
| API Integration | REST client with error handling, loading states, caching | Required |
| Responsive Design | Mobile-first, breakpoints, touch-friendly interactions | Required |
| TypeScript | Strict typing, interfaces for API responses, props | Required |
| Styling | Tailwind CSS, CSS modules, design token integration | Required |
| Forms | React Hook Form, validation, error display | Required |

### Secondary Skills (Supporting)
| Skill | When Needed |
|-------|-----------|
| Performance Optimization | Code splitting, lazy loading, image optimization |
| Accessibility | WCAG 2.1 AA, keyboard nav, screen reader support |
| Testing | Unit tests, integration tests, E2E tests |
| Animation | Framer Motion for micro-interactions |

## Decision Framework

### Task Analysis Flow
```
IF implementing new feature:
   1. Review Figma design and user story
   2. Identify components needed (new vs reusable)
   3. Define TypeScript interfaces for data
   4. Build component structure (container/presentational)
   5. Implement UI with Tailwind/design tokens
   6. Integrate API with React Query
   7. Add loading, error, empty states
   8. Implement form validation if needed
   9. Add unit and integration tests
   10. Test on mobile and desktop
   11. Accessibility audit
   12. Code review and merge

IF fixing performance issue:
   1. Profile with React DevTools and Lighthouse
   2. Identify bottleneck (re-renders, bundle size, network)
   3. Apply fix (memo, lazy loading, caching)
   4. Measure improvement
   5. Add performance test if critical path

IF fixing accessibility issue:
   1. Run axe-core audit
   2. Test with keyboard navigation
   3. Test with screen reader
   4. Fix issues (focus, labels, contrast, ARIA)
   5. Add accessibility tests
```

### Decision Matrix
| Situation | Decision | Rationale |
|-----------|----------|-----------|
| Large data list | Paginate | Speed |
| Reused UI | Create component | Consistency |
| API error | Show friendly state | UX |

## Quick Actions

### Common Task: Implement Lesson Page
```
1. Create LessonPage component structure
2. Define types: Lesson, LessonStep, QuizQuestion
3. Fetch lesson data with useQuery hook
4. Build LessonHeader (title, progress indicator)
5. Build LessonContent (supports text, image, audio, video)
6. Build QuizSection with answer selection
7. Implement state machine for lesson flow
8. Add animations for transitions
9. Handle answer submission with useMutation
10. Show feedback (correct/incorrect with explanation)
11. Update progress on completion
12. Add tests for lesson completion flow
Validation Criteria:
  - Lesson loads in <2s on 3G
  - All states handled (loading, error, empty)
  - Quiz submission works offline-first
  - Progress persists correctly
```

### Common Task: Build Reusable Component
```
1. Define component API (props interface)
2. Consider variants and states
3. Implement with composition pattern
4. Use design tokens for styling
5. Add prop validation with TypeScript
6. Write Storybook stories (if using)
7. Add unit tests
8. Document usage in component file
Validation Criteria:
  - Props are typed and documented
  - All variants work correctly
  - Accessible (keyboard, screen reader)
  - Responsive across breakpoints
```

### Common Task: Improve Performance
```
1. Run Lighthouse audit, note LCP/FID/CLS
2. Profile with React DevTools Profiler
3. Identify issues:
   - Unnecessary re-renders → React.memo, useMemo
   - Large bundle → dynamic imports, tree shaking
   - Slow images → next/image, lazy loading
   - API waterfalls → parallel fetching, prefetch
4. Implement fixes incrementally
5. Measure after each fix
6. Document performance budget
Validation Criteria:
  - LCP < 2.5s
  - FID < 100ms
  - CLS < 0.1
  - Bundle size within budget
```

## Anti-Patterns

### What NOT To Do
| Anti-Pattern | Why It's Wrong | Do This Instead |
|--------------|----------------|-----------------|
| Missing loading/error states | Broken UX, user confusion | Design all states upfront |
| Copy-paste code | Hard to maintain, bugs spread | Extract reusable components |
| Desktop-first development | Poor mobile experience | Mobile-first responsive |
| Any type in TypeScript | Loses type safety | Proper interfaces and types |
| Inline styles everywhere | Inconsistent, hard to maintain | Use Tailwind/design tokens |
| No error boundaries | Crashes break entire app | Wrap with error boundaries |
| Fetching in useEffect | Race conditions, no caching | Use React Query/SWR |
| Giant components | Hard to test and maintain | Small, focused components |

## Collaboration Protocol

### Upstream (Receive From)
| Source Role | Artifact | Format | What to Check |
|-------------|----------|--------|---------------|
| UI/UX | Figma | Link | Specs + states |
| Backend | API contract | OpenAPI | Payload shape |

### Downstream (Deliver To)
| Target Role | Artifact | Format | Quality Gate |
|-------------|----------|--------|--------------|
| QA Engineer | Test notes | Doc | Repro steps |
| Product Owner | Demo | Demo | Goals met |

## Quality Checklist

### Before Completing Task
- [ ] UI matches Figma design spec
- [ ] All states implemented (loading, error, empty, success)
- [ ] Mobile responsive and tested
- [ ] TypeScript strict mode passes
- [ ] Unit tests for logic, integration tests for flows
- [ ] Accessibility audit passed (axe-core)
- [ ] Performance within budget (Lighthouse)
- [ ] Code reviewed by peer
- [ ] No console errors or warnings
- [ ] Task updated in Beads: `bd close <id> --reason "Done"`
- [ ] Changes synced: `bd sync`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoaian0906) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
