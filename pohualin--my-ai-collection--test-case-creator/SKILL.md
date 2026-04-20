---
name: test-case-creator
description: Generate comprehensive test cases for functions, classes, or modules. This skill analyzes code and produces a variety of test scenarios, including edge cases, to ensure robust test coverage. Strictly follows language-specific specs. Use when this capability is needed.
metadata:
  author: pohualin
---

# Purpose
Create production-ready unit test cases for Python, Java, and React codebases, based on source files, function signatures, and user input. Strictly follow the language specification (e.g., java-spec.md, python-spec.md, react-spec.md) and validate against actual implementation and coverage reports.

# Process
1. Parse request: Extract language, target files, functions/classes, and user-supplied details
2. Detect frameworks and language version from build files (e.g., pom.xml, build.gradle, package.json, requirements.txt)
3. Generate tests using a strict checklist (see below)
4. Run coverage tools and iterate: Add tests for uncovered lines/branches until 95%+ coverage is achieved
5. Summarize coverage and justify any uncovered code

# Checklist for Every Method
- [ ] All public methods tested
- [ ] All logical branches (if/else, switch, loops)
- [ ] Exception/error paths
- [ ] Edge cases, invalid/null/empty/boundary inputs
- [ ] Parameterized tests for multiple scenarios (if supported)
- [ ] Integration tests for service/repository layers (if applicable)
- [ ] Business logic, data transformations, error handling
- [ ] Tests match actual method behavior (exceptions vs. error responses)
- [ ] Coverage reviewed and iterated until 95%+ achieved
- [ ] Summary of coverage and justification for any uncovered code

# Requirements
- Strictly follow the language-specific spec for all framework, version, and style requirements
- Fix all import and compatibility issues

# Example Usage
- "Write unit tests for my Python module according to python-spec.md."
- "Generate JUnit tests for my Java class, covering all branches and error handling as required by java-spec.md."
- "Create Jest tests for my React component."

# References
- [references/python-spec.md](references/python-spec.md): Python testing frameworks, structure, and examples
- [references/java-spec.md](references/java-spec.md): Java testing frameworks, structure, and examples
- [references/react-spec.md](references/react-spec.md): React testing frameworks, structure, and examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pohualin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
