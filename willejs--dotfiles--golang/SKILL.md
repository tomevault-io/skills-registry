---
name: golang
description: This skill provides patterns and best practices for using Go programming language, including idiomatic code structure, error handling, concurrency, and testing. Use when this capability is needed.
metadata:
  author: willejs
---

# Go patterns

## Basic rules
- Follow the [Effective Go](https://golang.org/doc/effective_go) guidelines for writing idiomatic Go code.
- Use `gofmt` to format your code consistently.
- Organize code into packages, each with a clear purpose.
- Use interfaces to define behavior and promote decoupling.
- Handle errors explicitly and avoid using panic for regular error handling.
- Write tests using the `testing` package and aim for high test coverage.
- For simple projects favour a simple structure that mirrors opensource projects, if the project grows or has complexity and lots of business logic consider using a more suitable pattern such as hexagonal (ports and adaptors) architecture
- Avoid complexity, keep functions small and focused on a single task.
- Avoid implementing patterns from other languages that don't fit well with Go's design philosophy, such as factory patterns or heavy use of inheritance.
- Use goroutines and channels for concurrency, but be mindful of potential pitfalls like race conditions.
- Document your code using Go's documentation conventions, including comments for exported functions and types, but dont explain the obvious and over document.
- Use go modules for dependency management.
- Regularly review and refactor code to improve readability and maintainability.
- Leverage the standard library as much as possible before reaching for third-party packages.
- Tag tests with build tags to separate unit, integration, and end-to-end tests where appropriate.
- When writing integration tests, use test containers or mocks to isolate external dependencies.
- when writing e2e tests, ensure they run against a real service, no mocks, by specifying a real endpoint.
- Ensure there is CI/CD that:
  - Runs tests, linters and static analysis (golangci-lint) on every commit.
  - Builds and validates the project (binaries, libraries, or containers as appropriate).
  - For containerized applications: publishes to a container registry and scans images for vulnerabilities before deployment.

## Language specific rules

## Rule 1: Respect Value Semantics
- All arguments are passed by value.
- Use pointers when mutating data or avoiding large copies.
- If any method mutates a struct, **all methods use pointer receivers**.

---

## Rule 2: Be Explicit About `nil`
- Do not return `nil` slices or maps from public APIs.
- Prefer empty slices (`[]T{}`) unless `nil` has meaning.
- Never assume `nil` and empty are interchangeable.

---

## Rule 3: Never Ignore Errors
- Do not discard returned errors.
- Wrap errors using `%w`.
- Return errors early with contextual messages.

---

## Rule 4: Use `defer` Correctly
- `defer` executes at function return, not block exit.
- Never `defer` inside loops unless wrapped in a function literal.
- Place `defer` immediately after resource acquisition.

---

## Rule 5: Fix Loop Variable Capture
- **Note**: Go 1.22+ fixed this issue by scoping loop variables per-iteration by default.
- For Go <1.22: Never capture loop variables directly in closures or goroutines.
- For Go <1.22: Always shadow loop variables inside the loop.

---

## Rule 6: Concurrency Requires Ownership
- Do not start goroutines without:
  - A clear owner
  - A termination condition
- Avoid concurrency unless it provides real benefit.
- Prefer simple, synchronous code by default.

---

## Rule 7: Always Respect `context.Context`
- If a function accepts `context.Context`, it must observe cancellation.
- Check `ctx.Done()` in loops and before blocking work.
- Never store contexts in structs.

---

## Rule 8: Maps Are Not Thread-Safe
- Never read and write maps concurrently without synchronization.
- Use `sync.Mutex`, `sync.RWMutex`, or justified `sync.Map`.
- Never rely on map iteration order.

---

## Rule 9: Prefer Go Idioms Over Patterns
- Avoid Java/C++ design patterns.
- Prefer composition over abstraction.
- Accept interfaces, return concrete types.
- Keep interfaces small.
- Use generics (Go 1.18+) for type-safe data structures, but don't over-abstract.
- Avoid using generics when a simple interface or concrete type would suffice.

---

## Rule 10: Optimize for Clarity, Not Cleverness
- Write boring, explicit code.
- Avoid over-engineering.
- Favor readability over brevity.
- Assume the code will be maintained by humans.

---

## Final Constraint (Hard Rule)
> If correctness, simplicity, and idiomatic Go conflict with cleverness or abstraction — **choose simplicity**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willejs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
