---
name: architecture-review
description: Comprehensive codebase architecture analysis with quality assessment and recommendations Use when this capability is needed.
metadata:
  author: finesseac
---

# Architecture Review Protocol

A systematic approach to analyzing and improving codebase architecture.

## Phase 1: Structure Mapping

Map the overall architecture:

- **Modules/Packages**: List all major components
- **Dependencies**: Map inter-module dependencies
- **Data Flows**: Document how data moves through the system
- **Integration Points**: External APIs, databases, third-party services
- **Entry Points**: Main endpoints, CLI commands, event handlers

## Phase 2: Quality Assessment

Rate each area on a scale of 1-5:

### Code Quality Metrics

- [ ] **Separation of Concerns** (1-5): Are responsibilities well-divided?
- [ ] **Code Reusability** (1-5): Is code DRY without being overly abstract?
- [ ] **Error Handling** (1-5): Are errors handled consistently and thoroughly?
- [ ] **Type Safety** (1-5): Are types used effectively?
- [ ] **Naming Conventions** (1-5): Are names clear and consistent?

### Architecture Metrics

- [ ] **Modularity** (1-5): Can components be changed independently?
- [ ] **Scalability** (1-5): Can the system grow without major rewrites?
- [ ] **Testability** (1-5): Is the code easy to test?
- [ ] **Maintainability** (1-5): Can new developers understand it?
- [ ] **Security Posture** (1-5): Are security best practices followed?

## Phase 3: Pattern Analysis

Identify patterns in use:

### Design Patterns Detected

- List each pattern found (Repository, Factory, Observer, etc.)
- Note where they're applied correctly
- Note where they could be better utilized

### Anti-Patterns Detected

- God objects/classes
- Tight coupling
- Circular dependencies
- Magic numbers/strings
- Deep nesting
- Code duplication

### Inconsistencies

- Naming convention violations
- Style inconsistencies
- Error handling variations
- Logging inconsistencies

## Phase 4: Technical Debt Inventory

Document technical debt:

### Critical (Fix Immediately)

- Security vulnerabilities
- Data integrity risks
- Performance blockers

### High Priority (Fix Soon)

- Scalability limitations
- Major code duplication
- Missing error handling

### Medium Priority (Plan to Fix)

- Inconsistent patterns
- Missing tests
- Documentation gaps

### Low Priority (Nice to Have)

- Minor style issues
- Optional optimizations
- Nice-to-have refactors

## Phase 5: Recommendations

Provide actionable recommendations:

### Immediate Actions

1. **[Action]** - Reason - Estimated effort
2. **[Action]** - Reason - Estimated effort
3. **[Action]** - Reason - Estimated effort

### Short-Term Improvements (1-2 weeks)

1. **[Improvement]** - Impact - Effort
2. **[Improvement]** - Impact - Effort

### Long-Term Initiatives (1+ months)

1. **[Initiative]** - Strategic value - Resources needed
2. **[Initiative]** - Strategic value - Resources needed

## Deliverable

Generate `ARCHITECTURE_REVIEW.md` containing:

1. Executive Summary
2. Architecture Diagram (text-based)
3. Quality Scores
4. Patterns Analysis
5. Technical Debt Inventory
6. Prioritized Recommendations
7. Risk Assessment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/finesseac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
