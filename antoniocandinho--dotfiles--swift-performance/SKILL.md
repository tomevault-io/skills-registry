---
name: swift-performance
description: Write and review Swift code with a focus on measurable performance, memory behavior, and concurrency correctness. Use when this capability is needed.
metadata:
  author: antoniocandinho
---

## What I do

- Write Swift that is fast, readable, and benchmark-friendly.
- Review Swift for algorithmic complexity, allocations, ARC churn, and dispatch costs.
- Prefer changes that are justified by likely hotspots or profiling evidence.

## Default assumptions

- Favor clarity first, then optimize obvious hot paths.
- Avoid micro-optimizations unless code is in tight loops or called frequently.
- If tradeoffs are unclear, present 2 options with costs.

## Writing rules (performance-oriented Swift)

### Generics across module boundaries
- If a public generic crosses a module boundary, call out specialization limits and potential performance impacts.
- Prefer small, inlineable generics to reduce cross-module overhead.
- Use `@_specialize` only as a last resort for hot generics that must remain public.

### Structs and classes

- Use `private` or `fileprivate` wherever possible.
- Mark types `final` when inheritance is not required to avoid dynamic dispatch.
- Prefer in-place mutation over creating new objects when safe and clear.
- Prefer `struct` for trivial value types; use `class` only when reference semantics are required.
- Be careful with large, non-trivial structs: copies can be expensive due to malloc/free and ARC overhead.
- For large, non-trivial data, consider copy-on-write; use a `Guts` reference type to hold shared storage.
- When conforming to protocols, consider the Value Witness Table and the inline buffer (3 words) in performance-sensitive paths.

### Closures

- Avoid capturing `var` in escaping closures; it triggers heap allocation.
- If a capturing closure is defined inside a function, consider `@inline(__always)` to remove overhead when appropriate.

### Data structures and algorithms

- Prefer `ContiguousArray` over `Array` for reference types to reduce unnecessary `NSArray` bridging.
- Call `reserveCapacity(_:)` when size is known or can be estimated.
- Avoid chaining `map`/`filter`/`reduce` without `lazy`; it creates intermediate arrays.
- Prefer plain `for` loops over lazy chaining in hot paths.
- Avoid `reduce(into:)` in favor of a `for` loop when optimizing hot paths.
- Avoid hash-based structures when `Hashable`/`Equatable` is expensive or keys are large structs.
- Minimize allocations on hot paths; prefer POD structs and upfront allocation when possible.
- Watch for accidental O(n^2) patterns (e.g., `contains`, `remove(at:)`, `firstIndex(of:)`) inside loops.

## Code review checklist

- Big-O: nested linear scans, repeated indexing searches, or repeated sorting?
- Allocations: temporary collections, repeated bridging to Foundation, unnecessary string interpolation?
- Copying: large struct copies, Data/Array growth without reserve, needless conversions?
- Dispatch: avoidable dynamic dispatch, cross-module generic overhead?
- Concurrency: actor isolation correctness, main-thread work, cancellation handling?

## References

- Swift stdlib: https://github.com/swiftlang/swift/tree/main/stdlib

## Output format when reviewing code

- Start with 3-6 high-impact findings.
- Include specific changes (snippets) and explain the performance mechanism.
- Call out anything that needs profiling to confirm.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/antoniocandinho) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
