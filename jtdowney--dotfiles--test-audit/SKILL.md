---
name: test-audit
description: Comprehensive test suite audit and rewrite workflow. Use when reviewing project tests, rewriting tests from scratch, improving test coverage, or consolidating redundant tests. Supports full project audits, module-by-module reviews, and post-implementation validation. Use when this capability is needed.
metadata:
  author: jtdowney
---

# Test Audit & Rewrite

Interactive workflow for auditing and rewriting project test suites.

## Principles

1. Test all code, but only *our* code—not the library or language
2. Prefer property-based tests for serialization, parsing, and validation patterns
3. Use snapshot tests for multi-line string outputs
4. Write only necessary tests—no redundancy
5. Each test should be small and focused

## Workflow

### Phase 1: Discovery

1. Identify test framework and existing patterns (snapshot lib, PBT lib, assert style)
2. Map source modules to their test files
3. Note coverage gaps and redundant tests

Present findings and discuss the audit scope before proceeding.

### Phase 2: Planning

For each module, propose:
- Tests to keep (with reason)
- Tests to consolidate or remove
- New tests needed
- PBT opportunities (encode/decode pairs, validators, parsers)
- Snapshot opportunities (structured output, error messages)

Discuss the plan interactively—adjust based on feedback.

### Phase 3: Execution

Work module-by-module:
1. Confirm the plan for current module
2. Rewrite tests
3. Run tests to verify
4. Move to next module

## Test Selection Heuristics

**Keep** tests that:
- Cover distinct behavior or edge case
- Document important invariants
- Catch real bugs that occurred

**Consolidate** tests that:
- Test the same code path with trivial variations
- Can be replaced by a single parameterized/table-driven test
- Can be replaced by a property-based test

**Remove** tests that:
- Test library/framework behavior
- Duplicate other tests
- Test implementation details that may change

## Language-Specific Patterns

See references for language-specific testing conventions:
- [references/gleam.md](references/gleam.md) - qcheck, birdie, assert style
- [references/rust.md](references/rust.md) - proptest, insta, mod test

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jtdowney) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
