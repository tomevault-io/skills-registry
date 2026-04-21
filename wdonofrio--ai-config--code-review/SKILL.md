---
name: code-review
description: Perform language-agnostic code reviews focusing on correctness, regressions, security, and tests. Use when the user asks for a review. Use when this capability is needed.
metadata:
  author: wdonofrio
---

# Code Review Workflow

Focus on defects, regressions, and risks first. Be specific and evidence-based.

## Review Priorities

Review in this order of importance:

1. **Correctness**: Does it do what it's supposed to?
2. **Security**: Are there vulnerabilities?
3. **Regressions**: Could this break existing behavior?
4. **Tests**: Is the change adequately tested?
5. **Maintainability**: Will this be easy to understand and modify?
6. **Performance**: Only if there are clear concerns.

## Review Process

### Step 1: Understand the Context

- Read the PR description and linked issues
- Understand the intent before reading code
- Ask clarifying questions if the purpose is unclear

### Step 2: Review for Correctness

- Trace the logic for the main use cases
- Identify edge cases: null, empty, max values, concurrent access
- Check error handling paths
- Verify data validation and sanitization

### Step 3: Review for Security

Check against `shared/guidelines/security.md`:
- Input validation present?
- Injection vulnerabilities (SQL, command, path)?
- Authentication/authorization correct?
- Sensitive data handled properly?

### Step 4: Review for Regressions

- Are existing tests still passing?
- Could this change affect unrelated functionality?
- Are there implicit dependencies being broken?

### Step 5: Review Tests

Check against `shared/guidelines/testing.md`:
- Is the new behavior tested?
- Are edge cases covered?
- Are the tests meaningful (not just coverage padding)?
- Can you think of test cases that are missing?

### Step 6: Review Maintainability

Check against `shared/guidelines/coding-style.md`:
- Is the code readable and self-documenting?
- Are there complex sections that need comments?
- Is there duplication that should be extracted?
- Does it follow project conventions?

## Output Format

Structure your review as:

### Critical Issues (Must Fix)
- Security vulnerabilities
- Logic errors
- Data loss risks

### Suggestions (Should Consider)
- Better approaches
- Missing edge cases
- Test improvements

### Questions
- Clarifications needed
- Design decisions to discuss

### Positive Notes (Optional)
- Well-designed solutions
- Good test coverage
- Clean implementations

## Review Etiquette

- **Be specific**: Point to exact lines, explain why
- **Suggest solutions**: Don't just point out problems
- **Ask, don't demand**: "What do you think about..." vs "You must..."
- **Separate opinions from requirements**: Style preferences vs bugs
- **Acknowledge good work**: Positive feedback helps

## Reminders

- Use `shared/guidelines/security.md` for security checklist
- Use `shared/guidelines/testing.md` for test coverage expectations
- Flag if tests weren't run and suggest how to run them

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wdonofrio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
