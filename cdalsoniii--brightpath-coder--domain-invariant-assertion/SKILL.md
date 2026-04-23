---
name: domain-invariant-assertion
description: Verify domain invariants hold across all code paths — balance non-negativity, zero-sum transfers, state machine transitions, and business rule enforcement Use when this capability is needed.
metadata:
  author: cdalsoniii
---

# Domain Invariant Assertion Skill

Systematically verify that domain invariants are enforced in code and covered by tests. Invariants are properties that must ALWAYS hold regardless of system state.

## Trigger Conditions
- Model struct or enum definitions change
- Service business logic is modified
- New financial operations are introduced
- User invokes with "check invariants" or "domain-invariant-assertion"

## Input Contract
- **Required:** Path to models and service directories
- **Optional:** Path to test files for coverage assessment

## Output Contract
- Inventory of all domain invariants found in the codebase
- Pass/fail assessment for each invariant's enforcement in code
- Pass/fail assessment for each invariant's test coverage
- Missing invariant identification (invariants that SHOULD exist but don't)

## Tool Permissions
- **Read:** All Go source files, test files, model files
- **Write:** None (read-only analysis)
- **Search:** Grep for assertion patterns, validation methods, constraint checks

## Execution Steps

   - Balance >= 0 (unless overdraft enabled)
   - Transfer amount > 0
   - Transfer source != destination
   - Account status transitions follow state machine
   - Card status transitions follow lifecycle
   - Currency is valid ISO 4217
   - Idempotency key is unique per operation
   - Balance-after = previous_balance +/- amount

2. **Verify code enforcement**: For each invariant, find the code path that enforces it. Flag invariants with no enforcement.

3. **Verify test coverage**: For each invariant, find property-based tests or unit tests that verify it. Flag invariants with no test coverage.

4. **Report**: Produce a table of invariants with enforcement status and test coverage status.

## Success Criteria
- All known domain invariants are enforced in code
- All known domain invariants have test coverage
- No code path can bypass an invariant

## References
- `.cursor/rules/111-property-based-testing.mdc`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cdalsoniii) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
