---
name: rspec-test-framework
description: Execute and generate RSpec tests for Ruby projects with let bindings, before hooks, and mocking support Use when this capability is needed.
metadata:
  author: fortiumpartners
---

# RSpec Test Framework

## Purpose

Provide RSpec test execution and generation for Ruby projects.

## Usage

```bash
ruby generate-test.rb --source=lib/calculator.rb --output=spec/calculator_spec.rb --description="Division by zero"
ruby run-test.rb --file=spec/calculator_spec.rb
```

## Output Format

JSON with success, passed, failed, total, and failures array.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fortiumpartners) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
