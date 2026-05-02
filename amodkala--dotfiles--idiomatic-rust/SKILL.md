---
name: idiomatic-rust
description: Rust code review patterns from corrode.dev (16 articles) Use when this capability is needed.
metadata:
  author: amodkala
---

## Overview

This skill provides guidance on writing idiomatic, production-ready Rust code based on 16 comprehensive articles from corrode.dev by Matthias Endler. Each pattern includes a summary and link to the full article, which you can fetch using the `webfetch` tool when you need detailed context.

## Pattern Summaries

### 1. Be Simple

Avoid over-engineering and premature abstraction. The biggest mistake is making code generic "because we might need it someday." Use concrete types unless you're actively switching implementations. Generics increase compile time, complexity, and cognitive load. Simple code is easier to debug, maintain, and often performs better because the compiler can optimize it more effectively. Don't be clever—be clear.

📖 https://corrode.dev/blog/simple/

### 2. Defensive Programming

Make the compiler enforce your invariants instead of relying on runtime checks. Destructure structs in trait implementations to catch when fields are added. Use slice pattern matching instead of indexing. Avoid `..Default::default()` because it hides which fields you're setting. Never use `From` implementations that can panic—use `TryFrom` instead. The goal is to make implicit assumptions explicit and compiler-checked.

📖 https://corrode.dev/blog/defensive-programming/

### 3. Pitfalls of Safe Rust

Memory safety doesn't mean your code can't have bugs. Check arithmetic with `checked_mul`, `checked_add`, etc. Never use `as` for narrowing conversions—use `TryFrom`. Don't index arrays directly—use `.get()` or slice patterns. Create newtype wrappers for domain concepts instead of using raw `String` or `u32`. Make illegal states unrepresentable with enums. Implement `Debug` carefully to avoid leaking secrets. Use constant-time comparison for passwords and sensitive data.

📖 https://corrode.dev/blog/pitfalls-of-safe-rust/

### 4. Prototyping in Rust

When prototyping, prioritize speed of iteration over perfection. Use liberal `unwrap()` and `clone()`. Don't worry about allocations. Accept performance crimes. The goal is to validate ideas quickly, then refactor with the knowledge you've gained. Rust is fast enough that your "slow" prototype is probably faster than other languages' production code anyway.

📖 https://corrode.dev/blog/prototyping/

### 5. Sharp Edges in Rust Std

The standard library has gotchas. `Path::join` with an absolute path replaces the base path entirely. `split_at` panics if the index is out of bounds—use `split_at_checked`. Some `Default` implementations create invalid states. Be aware that even safe Rust APIs can have surprising behavior.

📖 https://corrode.dev/blog/sharp-edges-in-rust-std/

### 6. Don't Unwrap Options

Stop using `unwrap()` and `expect()` in production code. Use `?` for propagation, `ok_or()` for conversion to Result, `unwrap_or()` for defaults, `map()` for transformation, and pattern matching for complex cases. Each approach communicates intent better than a blind unwrap. The only acceptable uses are in tests and when you've proven the invariant elsewhere in the code.

📖 https://corrode.dev/blog/rust-option-handling-best-practices/

### 7. Don't Worry About Lifetimes

Most of the time, you don't need explicit lifetimes. The compiler infers them correctly in 95% of cases. Only add lifetime annotations when the compiler can't figure out the relationship between references. Don't try to outsmart the borrow checker—listen to what it's telling you about your design. If you're fighting lifetimes, the problem is usually your API design, not the syntax.

📖 https://corrode.dev/blog/lifetimes/

### 8. Navigating Programming Paradigms

Rust supports multiple paradigms—imperative, functional, and object-oriented. Use the right tool for the job. Iterators and pattern matching are functional. Structs with methods are OOP. Loops and mutation are imperative. Don't force one paradigm everywhere. The best Rust code mixes paradigms appropriately based on the problem being solved.

📖 https://corrode.dev/blog/paradigms/

### 9. Thinking in Expressions

Rust is expression-oriented. Use `if`, `match`, and blocks as expressions that return values. This leads to more declarative code with fewer intermediate variables and mutations. Instead of setting a variable in each branch, return the value from the expression. This style prevents uninitialized variables and makes data flow clearer.

📖 https://corrode.dev/blog/expressions/

### 10. Aim For Immutability

Prefer `let` over `let mut`. Immutability prevents entire classes of bugs and makes code easier to reason about. When you need temporary mutability for initialization, shadow the variable to make it immutable afterward. Immutable data can be safely shared across threads. The compiler optimizes immutable code better because it knows the values can't change.

📖 https://corrode.dev/blog/immutability/

### 11. Thinking in Iterators

Replace loops with iterator chains when possible. Iterators are lazy, composable, and often compile to the same code as manual loops—but they're more declarative. Use `filter`, `map`, `flat_map`, `take`, `skip`, and other adapters to express intent clearly. The only time to use explicit loops is when the iterator version is genuinely less readable or when you need early returns.

📖 https://corrode.dev/blog/iterators/

### 12. Using Enums to Represent State

Enums are more powerful in Rust than in most languages. Use them to model state machines, eliminate boolean flags, and make illegal states unrepresentable. Each variant can carry different data. Match expressions force you to handle all cases. This prevents entire categories of bugs at compile time.

📖 https://corrode.dev/blog/enums/

### 13. Don't Use Preludes and Globs

Avoid `use module::*` and minimize prelude usage. Glob imports make code harder to understand because you can't tell where names come from. They also cause name conflicts when modules add new exports. Explicit imports make dependencies clear and help with code navigation. The small amount of extra typing is worth it for maintainability.

📖 https://corrode.dev/blog/dont-use-preludes-and-globs/

### 14. Compile-Time Invariants

Use the type system to enforce constraints at compile time instead of runtime. Create newtype wrappers that validate data in their constructors. Use const generics for array sizes. Encode state in types so invalid transitions don't compile. The more you can prove at compile time, the fewer runtime checks and tests you need.

📖 https://corrode.dev/blog/compile-time-invariants/

### 15. Make Illegal States Unrepresentable

Design types so invalid combinations are impossible to construct. Instead of a struct with `ssl: bool` and `ssl_cert: Option<PathBuf>`, use an enum with `Insecure` and `Ssl { cert: PathBuf }` variants. This way the compiler prevents you from having SSL enabled with no certificate. Good type design eliminates whole categories of bugs.

📖 https://corrode.dev/blog/illegal-state/

## Review Checklist

Use this checklist to systematically review code. For each issue found, fetch the relevant blog article with `webfetch` to provide detailed context in your explanation.

### 🔴 Critical Issues (P0) - Fix Immediately

- [ ] Unchecked arithmetic operations (multiplication, addition, subtraction that could overflow)
- [ ] Using `as` for narrowing type conversions instead of `TryFrom`
- [ ] Panicking code: `unwrap()`, `expect()`, `index[]` without clear justification
- [ ] Direct array indexing without bounds checking
- [ ] TOCTOU (time-of-check to time-of-use) race conditions
- [ ] Type design allows invalid state combinations
- [ ] Unsafe code blocks without proper safety documentation
- [ ] Sensitive data compared with `==` instead of constant-time comparison
- [ ] Unbounded input without size limits

### 🟡 Important Issues (P1) - Should Fix

- [ ] Premature abstraction or over-use of generics
- [ ] Boolean function parameters (use enums instead)
- [ ] Non-exhaustive match patterns with `_` catch-all
- [ ] Missing `#[must_use]` on important return types
- [ ] `From` implementations that can fail (should be `TryFrom`)
- [ ] Lazy use of `Default` trait (using `..Default::default()`)
- [ ] Primitive types for domain concepts (raw `String`, `i32` instead of newtypes)
- [ ] Fragile trait implementations that don't destructure structs

### 🟢 Suggestions (P2) - Consider Improving

- [ ] Loops that could be replaced with iterator methods
- [ ] Unnecessary mutability (`let mut` when value never changes)
- [ ] Using statements where expressions would be clearer
- [ ] Code that could be simpler without sacrificing correctness
- [ ] Missing temporary mutability pattern (init then shadow to immutable)
- [ ] Opportunities to leverage type system more effectively

## Instructions for Review

1. **Load this skill** to see pattern summaries and the checklist
2. **When you identify an issue**, fetch the full article using `webfetch`:
   ```
   webfetch("https://corrode.dev/blog/simple/")
   ```
3. **Extract relevant sections** from the article to provide detailed context
4. **Reference the article** in your finding with specific section links
5. **Provide code examples** showing both the problem and the recommended fix

## Usage Example

When you find code using `as` for casting:
1. Identify from checklist: "Using `as` for narrowing conversions"
2. Fetch article: `webfetch("https://corrode.dev/blog/pitfalls-of-safe-rust/")`
3. Find the section on type casting
4. Explain the issue with context from the article
5. Provide the recommended `TryFrom` approach

Remember: The blog articles contain detailed explanations, code examples, and rationale. Use them to provide educational, thorough feedback.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amodkala) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
