---
name: elixir-ash-reviewer
description: Comprehensive code review system for Elixir applications using Phoenix, LiveView, Ash Framework, LiveSvelte, TailwindCSS, and DaisyUI. Performs deep analysis of code quality, architectural patterns, test coverage, and Ash-specific best practices. Use when (1) Reviewing Elixir/Phoenix/Ash codebases for quality and best practices, (2) Analyzing test suite quality and brittleness, (3) Ensuring Ash framework patterns are properly implemented, (4) Identifying architectural improvements, (5) Reviewing LiveView/LiveSvelte component structure, (6) Validating resource design and API patterns Use when this capability is needed.
metadata:
  author: oxmus
---

# Elixir/Ash Stack Code Reviewer

Expert code review system for modern Elixir applications using Ash Framework, Phoenix, LiveView, and associated technologies.

## Review Process

### Phase 1: Codebase Analysis
1. Run `scripts/analyze_codebase.py` to get structure overview
2. Identify Ash resources, domains, and extensions
3. Map LiveView/LiveSvelte components
4. Catalog test coverage areas

### Phase 2: Ash Framework Review

#### Resource Design
Review each Ash resource for:
- **Attributes**: Type safety, constraints, defaults
- **Actions**: CRUD completeness, custom actions appropriateness
- **Calculations**: Efficiency, loading strategy
- **Relationships**: Proper associations, join keys
- **Policies**: Authorization completeness, performance
- **Changes**: Hook placement, transaction boundaries
- **Validations**: Business rule coverage

#### Domain Architecture
- Domain boundaries and cohesion
- Public vs private resource exposure
- Extension usage (AshPhoenix, AshGraphql, AshJsonApi)
- Multi-tenancy implementation if applicable

For detailed Ash patterns, load: `references/ash-patterns.md`

### Phase 3: Test Suite Analysis

#### Ash-Specific Testing
- Resource factory usage with ExMachina/Smokestack
- Changeset and action testing
- Policy testing with different actors
- Calculation and aggregate testing
- API boundary testing

#### Test Quality Indicators
- Setup/teardown patterns
- Async test safety
- Database sandbox usage
- Mock/stub appropriateness

For test standards, load: `references/test-standards.md`

### Phase 4: LiveView/LiveSvelte Review

#### Component Architecture
- State management patterns
- PubSub usage and event handling
- Form changesets with AshPhoenix.Form
- Socket assigns optimization
- JavaScript hooks integration

#### LiveSvelte Integration
- Props passing patterns
- Store management
- Server/client state sync
- Bundle size considerations

### Phase 5: Performance & Security

#### Ash Performance
- N+1 query detection
- Load statement optimization
- Pagination implementation
- Bulk action usage
- Cache strategy

#### Security Review
- Policy coverage gaps
- Input validation
- SQL injection prevention
- Authorization bypass risks
- Sensitive data exposure

### Phase 6: Code Quality Metrics

#### Style & Conventions
- Credo compliance
- Formatter consistency
- Module organization
- Function complexity
- Documentation coverage

#### Architecture Patterns
- Context boundaries
- Dependency direction
- Interface segregation
- Error handling patterns

For common issues, load: `references/common-issues.md`

## Review Output Format

Structure findings as:

```markdown
# Code Review Report

## Executive Summary
- Overall health score
- Critical issues count
- Key recommendations

## Critical Issues
[Issues requiring immediate attention]

## Ash Framework Compliance
[Resource design and pattern adherence]

## Test Suite Assessment
[Coverage, quality, brittleness factors]

## Performance Concerns
[Query optimization, memory usage]

## Security Findings
[Authorization, validation gaps]

## Code Quality
[Style, maintainability metrics]

## Recommendations
[Prioritized improvement plan]
```

## Quick Commands

```elixir
# Analyze Ash resources
mix ash.list_resources

# Check test coverage
mix test --cover

# Run Credo analysis
mix credo --strict

# Analyze dependencies
mix deps.tree

# Check for security issues
mix sobelow
```

## Resource Usage

- **Initial analysis**: Run `scripts/analyze_codebase.py`
- **Ash patterns**: Load `references/ash-patterns.md` for deep dives
- **Test standards**: Load `references/test-standards.md` for test review
- **Common issues**: Load `references/common-issues.md` for antipattern detection

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxmus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
