---
name: rust-clippy-clean
description: Rust com clippy warnings-as-errors, sem unwrap fora de testes Use when this capability is needed.
metadata:
  author: alanpcf
---
Antes de declarar pronto:
- `cargo fmt`
- `cargo clippy --all-targets --all-features -- -D warnings`
- `cargo test`

Regras:
- Evite `unwrap()` e `expect()` fora de testes e `fn main()`. Use `?` com `anyhow::Result` em apps, `thiserror` em libs.
- `unsafe` proibido sem comentário `// SAFETY: …` explicando invariantes.
- Prefira `&str` a `String` em parâmetros quando não precisar de ownership.
- Derive `Debug`, `Clone`, `PartialEq` por default; remova só se tem razão.

---
> Source: [alanpcf/cocada](https://github.com/alanpcf/cocada) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
