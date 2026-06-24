---
name: rust-failure-modes
description: Rules for writing or editing Rust code. Forbids #[allow(clippy::*)] without justification, .clone() to escape the borrow checker, .unwrap()/.expect() outside test code. Auto-loads on .rs file edits. Use when this capability is needed.
metadata:
  author: junhjang
---

Apply these rules when touching `.rs` files. Full rationale: [`.claude/rules/rust-llm-failures.md`](../../rules/rust-llm-failures.md).

1. **No `#[allow(clippy::*)]` without `// reason: ...`.** Fix the lint first; suppress only with documented justification next to the attribute.
2. **No `.clone()` to escape the borrow checker.** Try `&T`, lifetime restructuring, ownership change, `Cow<T>`, or `Arc<T>` first. "Compiler complained" is not a reason.
3. **No `.unwrap()` / `.expect()` outside `#[cfg(test)]` or `tests/`.** Use `?` and `Result<T, E>`. Exceptions: provably impossible invariant with `// reason:` comment, or initialization-time unrecoverable failure (intentional panic). Performance-critical and safety-critical code: zero tolerance.

If tempted to break any rule, stop and ask the user. Do not silently bypass with a suppression.

---
> Source: [junhjang/claude-harness](https://github.com/junhjang/claude-harness) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
