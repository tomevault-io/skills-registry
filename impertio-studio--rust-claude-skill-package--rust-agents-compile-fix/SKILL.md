---
name: rust-agents-compile-fix
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# Rust Agents Compile-Fix

This skill is the procedure for iterating on `rustc` compile errors. It does ONE job: take a failing `cargo build` / `cargo check` and converge on a clean compile, deliberately and without cascading edits.

ALWAYS use `cargo check` (not `cargo build`) while iterating: it skips codegen and is faster. ALWAYS recompile after each fix. NEVER batch-fix many errors and recompile once.

This skill iterates on errors. It does NOT route tasks (that is `rust-agents-orchestrator`) and does NOT do deep idiom review (that is `rust-agents-code-reviewer`). For the meaning of a specific error class, hand off to the relevant `rust-errors-*` skill.

## The Compile-Fix Loop

```
1. cargo check                          # get the full error list
2. read the FIRST error top to bottom   # earliest in compile order
3. classify the error (see decision tree)
4. apply ONE fix
5. cargo check again                    # re-evaluate from scratch
6. repeat from step 2 until clean
```

ALWAYS fix the earliest error first. Later errors are frequently cascades of the first: a single missing `use` or wrong type can produce ten downstream errors that all vanish when the root is fixed. NEVER trust the error count until the first error is resolved.

ALWAYS treat "more than 2 to 3 failed attempts on the same error" as a signal to STOP and ask the user. Repeated failure means the fix is a design change, not a one-liner.

## How to Read a rustc Error

A `rustc` diagnostic has a fixed shape. Read it top to bottom.

```
error[E0382]: borrow of moved value: `v`          <- 1. error code + headline
  --> src/main.rs:4:20                             <- 2. primary location
   |
 2 |     let v = vec![1, 2, 3];
   |         - move occurs because `v` has type ... <- 3. secondary span (context)
 3 |     let w = v;
   |             - value moved here                 <- 3. secondary span (the cause)
 4 |     println!("{:?}", v);
   |                      ^ value borrowed here after move  <- 4. primary span (the ^^^)
   |
   = note: ...                                      <- 5. note: extra context
help: consider cloning the value ...                <- 6. help: a candidate fix
   |
 3 |     let w = v.clone();
   |              ++++++++
```

| Part | What it is | How to use it |
|------|-----------|---------------|
| `error[EXXXX]` | the error code and one-line summary | the headline; look up the code if unfamiliar |
| `--> file:line:col` | primary location | where to start looking |
| `^^^` underline | the **primary span** | the exact code rustc objects to |
| `-` underlines | **secondary spans** | related code that explains the cause; the cause is often here, NOT at the primary span |
| `= note:` | extra context | read it; it often states the rule being violated |
| `help:` | a **candidate fix** | sometimes correct, sometimes wrong for the intent (see below) |

NEVER fix only the primary span without reading the secondary spans. For move and borrow errors the root cause is almost always at a secondary span (the move site), not the primary span (the use site).

## Decision Tree: How to Respond to an Error

```
rustc error in hand
├── Unfamiliar error code EXXXX?
│   └── run `rustc --explain EXXXX` FIRST, then continue
│
├── Does rustc print a `help:` with a concrete suggestion?
│   ├── Mechanical suggestion?  (add `;`, add `&`, add `use`, add `mut`,
│   │   add `.await`, add `#[derive(...)]`, add `?`)
│   │   └── APPLY it. These are almost always correct.
│   │
│   └── Judgement suggestion?  (add a lifetime `'a`, add a trait bound,
│       "consider changing the type of ...", "consider borrowing here")
│       └── INSPECT it. Ask: does this match the design intent?
│           ├── Yes, it is a genuine omission ........... APPLY
│           └── No, it papers over a design issue ....... STOP, see below
│
├── No `help:`, but a known one-line fix? (see catalog)
│   └── APPLY the one-line fix
│
├── Fix requires a local refactor (one function / one file)?
│   └── Apply the smallest refactor (see refactors), recompile
│
└── Fix would ripple across modules, change a public API,
    or you have already tried 2-3 times on this same error?
    └── STOP. Do NOT cascade edits. Ask the user (see "When to Stop").
```

## When to Apply a Suggestion Blindly vs Inspect It

`rustc` suggestions are not all equal.

**APPLY immediately (mechanical, machine-applicable):**

- `help: add a semicolon`
- `help: consider importing this` / missing `use`
- `help: consider borrowing here` when it adds a plain `&` to satisfy a `&str` / `&[T]` parameter
- `help: consider making this binding mutable: mut x`
- `help: consider awaiting this` / missing `.await`
- `help: consider using the \`?\` operator`
- `help: consider annotating ... with \`#[derive(Debug)]\`` when `Debug` is genuinely wanted

These are produced by `rustc` with the `MachineApplicable` confidence level and are exactly what `cargo fix` applies.

**INSPECT before applying (judgement required):**

- Lifetime suggestions (`help: consider introducing a named lifetime parameter`). `rustc` proposes the lifetime relationship that makes the code type-check, which can be MORE restrictive than the design needs (for example tying an output to an input that should be independent). Verify the proposed lifetime expresses the real ownership relationship. See [[rust-errors-lifetimes]].
- Trait-bound suggestions (`help: consider restricting type parameter ... with \`T: Clone\``). Adding a bound to silence `E0277` can push a constraint onto every caller. Confirm the bound belongs in the signature rather than indicating the wrong type was used. See [[rust-errors-trait-bounds]].
- `help: consider changing the type of ...`. Changing a type to satisfy the checker can be the right fix or can hide that the caller passed the wrong thing.
- Any suggestion that adds `.clone()`. Sometimes correct; often a sign the ownership structure is wrong. See [[rust-errors-borrow-checker]].

NEVER apply a lifetime or trait-bound suggestion without confirming it matches the design intent. A suggestion makes the code compile; it does not guarantee the code is correct.

## `rustc --explain`

When an error code is unfamiliar, run `rustc --explain EXXXX` (for example `rustc --explain E0499`). It prints the canonical explanation of the error class with a minimal reproduction and the standard fixes. ALWAYS do this before guessing at an unfamiliar code. The same content is the per-code page in the official error index.

## One-Line Fix Catalog

The most common compile errors have a one-token fix. ALWAYS check this catalog before reaching for a refactor.

| Symptom | Fix | Notes |
|---------|-----|-------|
| `cannot assign twice` / `cannot borrow as mutable` on a `let` | add `mut`: `let mut x = ...` | binding was immutable |
| `mismatched types: expected &T, found T` | add `&` (or `&mut`) at the call site | passing owned where borrow expected |
| `cannot find ... in this scope` (E0425/E0412/E0433) | add the missing `use` | apply the `help:` import suggestion |
| `the trait \`Debug\` is not implemented` | add `#[derive(Debug)]` to the type | also `Clone`, `PartialEq`, `Default` as needed |
| `\`?\` couldn't convert` / unhandled `Result` | add `?` (or `.unwrap()` in a test/example only) | function must return a compatible `Result` |
| `\`async fn\` ... is not awaited` / future unused | add `.await` | calling an async fn does not run it |
| `type annotations needed` (E0282/E0283) | add a turbofish `::<T>()` or annotate the `let` | inference has no anchor |
| `mismatched types: expected (), found T` (trailing) | remove or keep the `;` on the final expression | semicolon discards the value |
| `unused import` warning blocking `-D warnings` | remove the `use`, or run `cargo fix` | mechanical |

## Common Refactors (When One Line Is Not Enough)

When no one-liner applies, prefer the SMALLEST local refactor that resolves the root cause.

- **Introduce an intermediate `let` binding.** Splits a chained temporary so a borrow and a use no longer overlap. Fixes many `E0716` (temporary dropped) and `E0502` cases.
- **Take an argument by value instead of by reference** (or the reverse). If a borrow's lifetime cannot be satisfied, owning the value removes the lifetime entirely. If cloning to own is expensive, borrowing is right; choose by cost.
- **Split a function.** A function the borrow checker rejects because two borrows overlap can often be split so each borrow lives in its own function and scope.
- **Restructure the ownership tree.** Move a field, change who owns a value, or wrap a shared value in `Rc` / `Arc`. This is the largest of the "local" refactors; if it spreads beyond one module, STOP (see below).
- **Reorder statements.** Moving a `drop` or a use earlier/later can end a borrow before the conflicting one starts (non-lexical lifetimes already do much of this, but explicit ordering still helps).

ALWAYS recompile after each refactor. A refactor can expose a different error; that is progress, not failure.

## When to STOP and Ask the User

Some errors are not bugs to patch; they are the compiler reporting a design decision that has not been made. In these cases STOP and ask the user. Do NOT cascade edits.

STOP when:

- A fix would **ripple across multiple modules** (changing one function's ownership forces edits in callers in other files).
- A lifetime error can only be resolved by **redesigning an API or signature** (adding lifetime parameters to a public type, changing a trait method).
- A fix would **change a public API** (a `pub fn` signature, a `pub struct` field, a trait definition). The user owns API decisions.
- You have **tried the same error 2 to 3 times** without convergence. Repeated failure means the fix is structural.
- The only way forward is **adding `.clone()` in several places** to silence the borrow checker. This masks a design problem; surface it instead. See `references/anti-patterns.md`.

When stopping, report to the user: the exact error, the root cause as you understand it, and 2 to 3 concrete design options with their trade-offs. NEVER pick a public-API change unilaterally.

## `cargo fix` and `cargo clippy --fix`

`cargo fix` applies all `MachineApplicable` `rustc` suggestions automatically. `cargo clippy --fix` does the same for clippy lints. Use them for bulk mechanical cleanup (unused imports, edition idioms, simple style lints).

ALWAYS commit (or stash) a clean working tree BEFORE running either command. They rewrite source in place; with a dirty tree you cannot tell their changes from yours in `git diff`. Use `cargo fix --edition` only when deliberately migrating editions. NEVER run `cargo fix` as a substitute for reading a hard error: it will not resolve borrow, lifetime, or trait-design errors.

## Quick Reference

- Iterate with `cargo check`, recompile after EVERY fix, fix the EARLIEST error first.
- Read the diagnostic top to bottom: code, primary span, secondary spans, note, help.
- The root cause is often a secondary span (`-`), not the primary span (`^^^`).
- Apply mechanical `help:` suggestions; INSPECT lifetime and trait-bound suggestions.
- Run `rustc --explain EXXXX` for any unfamiliar code.
- `cargo fix` needs a clean tree first; it fixes mechanical lints, not design errors.
- More than 2 to 3 iterations on one error, or a cross-module / public-API ripple: STOP and ask the user.

## Reference Links

- Rust Compiler Error Index: https://doc.rust-lang.org/error_codes/error-index.html
- rustc command-line arguments (`--explain`, `--error-format`): https://doc.rust-lang.org/rustc/command-line-arguments.html
- `cargo fix`: https://doc.rust-lang.org/cargo/commands/cargo-fix.html
- `references/methods.md`: the loop in detail, the suggestion-confidence model, command flags
- `references/examples.md`: worked compile-fix sessions, one-liners, refactors, a stop-and-ask case
- `references/anti-patterns.md`: bottom-up fixing, batch-fixing, blind lifetime suggestions, clone-cascading
- Related: [[rust-errors-borrow-checker]] [[rust-errors-lifetimes]] [[rust-errors-trait-bounds]] [[rust-agents-orchestrator]] [[rust-agents-code-reviewer]] [[rust-core-toolchain]]

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
