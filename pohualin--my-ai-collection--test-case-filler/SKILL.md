---
name: test-case-filler
description: Automatically identify code with less than 100% test coverage and generate targeted test cases to fill coverage gaps. Integrates with test-coverage-checker and test-case-creator. Use when this capability is needed.
metadata:
  author: pohualin
---

# Purpose
Automate the process of achieving full test coverage by:
- Running coverage analysis
- Identifying files/functions with less than 100% coverage
- Generating new or improved test cases for uncovered code

# Process
1. Run test-coverage-checker to generate coverage report
2. Parse coverage report to find files/functions below 100% coverage
3. Use test-case-creator to generate targeted test cases for those files/functions
4. Optionally, repeat until coverage is maximized

# Output Format
---
name: test-case-filler
description: Automatically identify code with less than 100% test coverage and generate targeted test cases to fill coverage gaps.
---

# Test Case Filler Skill

The agent can assist with the following tasks:

## Coverage Analysis
- Run coverage report and identify gaps
- List files/functions with less than 100% coverage

## Test Generation
- Generate new or improved test cases for uncovered code
- Integrate with test-case-creator for language-specific test generation

## Automation
- Provide scripts or workflows to automate coverage analysis and test creation
- Optionally, suggest CI/CD integration for continuous coverage improvement

# Example Usage
- "Fill test coverage gaps in my project."
- "Generate tests for files below 100% coverage."
- "Automate coverage-driven test creation."

# References
- [test-coverage-checker](../test-coverage-checker/SKILL.md): Coverage analysis and reporting
- [test-case-creator](../test-case-creator/SKILL.md): Test case generation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pohualin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
