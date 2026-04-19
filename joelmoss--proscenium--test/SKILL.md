---
name: test
description: Run Ruby and/or Go tests. Pass a file path to run a specific test, or no args to run all. Use when this capability is needed.
metadata:
  author: joelmoss
---

# Test Runner

Run tests for Proscenium.

## Usage

- `/test` — Run all Ruby and Go tests
- `/test test/builder_test.rb` — Run a specific Ruby test file
- `/test test/builder_test.rb -n test_method_name` — Run a specific Ruby test method
- `/test test/builder_test.rb:10` — Run a specific Ruby test at line number
- `/test ./test -run TestBuild` — Run specific Go tests

## Steps

1. Determine what to run based on arguments:
   - **No args**: Run both Go and Ruby tests
   - **`.rb` file or Ruby-style args**: Run Ruby tests
   - **Go path/pattern**: Run Go tests
2. For Ruby tests, compile the Go binary first: `bundle exec rake compile:local`
3. Run the appropriate command:
   - **Ruby**: `bundle exec rails test <args>`
   - **Go**: `go test <args>`
4. Report results

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joelmoss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
