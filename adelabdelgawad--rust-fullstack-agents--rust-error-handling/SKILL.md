---
name: rust-error-handling
description: Define, map, and propagate errors in Rust+Leptos+Axum+SQLx projects using thiserror, anyhow, IntoResponse, and FromServerFnError. Trigger keywords: error, AppError, thiserror, anyhow, Result, unwrap, propagate, 500, 404, 422, map_err, Box<dyn Error>, ServerFnError, IntoResponse. Auto-triggers on ANY new domain module, server fn, or Axum handler that can fail; on any compile error about missing From impls or ? operator incompatibility; on any handler returning a raw String error or leaking internal details to the HTTP response. Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Rust Error Handling

## When to Use

- When generating any new domain module, server fn, or Axum handler that can fail.
- When receiving a compile error about missing `From` impls or `?` operator incompatibility.
- When a handler returns a raw `String` error or leaks internal details to the HTTP response.
- When wiring a new error type into the Leptos server fn error chain.
- NOT for panics in tests — `unwrap()` is acceptable in `#[cfg(test)]` blocks.

---

## Core Patterns

### 1. Domain Error Enum (`thiserror` 2.x)

Place in `src/error.rs` (or per-module `src/domain/error.rs`). This is the single source
of truth for all fallible states in the application.

```rust
// src/error.rs
use thiserror::Error;

#[derive(Error, Debug)]
pub enum AppError {
    #[error("not found")]
    NotFound,

    #[error("forbidden")]
    Forbidden,

    #[error("conflict on {0}")]
    Conflict(String),

    #[error("validation: {0}")]
    Validation(String),

    #[error("db error")]
    Db(#[from] sqlx::Error),

    #[error("internal")]
    Internal,
}
```

`#[from]` auto-generates `impl From<sqlx::Error> for AppError`, enabling `?` at every
SQLx call site without an explicit `.map_err(AppError::Db)`.
> **Book anchor — ch09-02 Recoverable Errors with Result:** the `?` operator desugars to
> `From::from(e)` — which is precisely what `#[from]` provides here.
> https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html

```rust
// ❌ Manual From impls — verbose, easy to forget, duplicated for every error type
impl From<sqlx::Error> for AppError {
    fn from(e: sqlx::Error) -> Self { AppError::Db(e) }
}
```

Use `#[source]` (instead of `#[from]`) when you want to chain cause info without
generating `From<T>`:

```rust
#[error("mailer failed")]
Mailer(#[source] lettre::transport::smtp::Error),
```

### 2. HTTP Mapping via `IntoResponse`

Wire once in `src/error.rs`; never map errors inside individual handlers.

```rust
use axum::{
    http::StatusCode,
    response::{IntoResponse, Response},
    Json,
};
use serde_json::json;

impl IntoResponse for AppError {
    fn into_response(self) -> Response {
        let (status, message) = match &self {
            AppError::NotFound      => (StatusCode::NOT_FOUND, "not found"),
            AppError::Forbidden     => (StatusCode::FORBIDDEN, "forbidden"),
            AppError::Conflict(_)   => (StatusCode::CONFLICT, "conflict"),
            AppError::Validation(m) => (StatusCode::UNPROCESSABLE_ENTITY, m.as_str()),
            AppError::Db(_)
            | AppError::Internal    => {
                tracing::error!(error = ?self, "internal error");
                (StatusCode::INTERNAL_SERVER_ERROR, "internal server error")
            }
        };
        (status, Json(json!({ "error": message }))).into_response()
    }
}
```

```rust
// ❌ Leaks internal details to the HTTP response body
impl IntoResponse for AppError {
    fn into_response(self) -> Response {
        (StatusCode::INTERNAL_SERVER_ERROR, self.to_string()).into_response()
    }
}
```

**Rule**: never leak `sqlx::Error` or stack traces to the client body. Log via
`tracing::error!` before mapping to 500.

### 3. Server Fn Mapping

The simplest, always-correct pattern is to return `Result<T, ServerFnError>` from `#[server]`
fns and map `AppError` at the call boundary:

```rust
// ✅ Simple pattern — what the nexa project uses
#[server]
pub async fn get_user(id: Uuid) -> Result<UserDto, ServerFnError> {
    let user = find_user(id).await
        .map_err(|e| ServerFnError::new(e.to_string()))?;
    Ok(UserDto::from(user))
}
```

```rust
// ❌ Returning AppError directly without the correct bridge impl
#[server]
pub async fn get_user(id: Uuid) -> Result<UserDto, AppError> {
    // Compile error unless FromServerFnError is correctly implemented
}
```

**Advanced: `FromServerFnError` for typed errors (Leptos 0.8 / server_fn 0.8.x)**

If you need `#[server]` fns to return `Result<T, AppError>` without mapping at each call
site, implement `FromServerFnError`. The Leptos 0.8 trait signature requires:

- Method takes `ServerFnErrorErr` (the concrete enum), **not** `ServerFnError`
- An associated `type Encoder` that satisfies `Encodes<Self> + Decodes<Self>`
- `AppError` must implement `Serialize + Deserialize`

```rust
use leptos::server_fn::error::{FromServerFnError, ServerFnErrorErr};
use server_fn::codec::JsonEncoding;

// AppError must be Serialize + Deserialize for the encoder to work
impl FromServerFnError for AppError {
    type Encoder = JsonEncoding;

    fn from_server_fn_error(value: ServerFnErrorErr) -> Self {
        tracing::error!("server fn error: {value:?}");
        AppError::Internal
    }
}
```

Wire this impl once; it applies to all server fns in the crate. The simpler
`.map_err(|e| ServerFnError::new(e.to_string()))` pattern avoids this complexity
and is preferred unless typed error dispatch on the client is required.

### 4. `anyhow` — Application Glue Only

`anyhow` is reserved for `main.rs`, CLI binaries, and one-shot startup code where
collecting heterogeneous errors is more valuable than typed dispatch.

```rust
// main.rs
use anyhow::{Context, Result};

#[tokio::main]
async fn main() -> Result<()> {
    let cfg = Config::from_env().context("failed to load config")?;
    let pool = PgPool::connect(&cfg.database_url)
        .await
        .with_context(|| format!("cannot connect to {}", &cfg.database_url))?;
    // ...
    Ok(())
}
```

```rust
// ❌ anyhow in domain/handler — not IntoResponse, breaks typed dispatch
// src/services/campaign.rs
use anyhow::Result;
pub async fn activate_campaign(id: Uuid) -> Result<Campaign> { ... }
```

**Convention** (community): "thiserror for libraries, anyhow for applications." This is
widely endorsed by both crate maintainers but is a community convention, not a language
rule. In a Leptos app the boundary is: `AppError` (thiserror) for all domain + handler
code; `anyhow::Result` only in `main.rs` startup / migration runner / CLI.

### 5. Handler Usage Pattern

```rust
// ❌ Mapping errors manually inside the handler — duplicated across every handler
pub async fn get_user(
    State(repo): State<UserRepo>,
    Path(id): Path<uuid::Uuid>,
) -> Result<Json<UserDto>, AppError> {
    let user = repo.find_by_id(id).await
        .map_err(AppError::Db)?;
    let user = user.ok_or(AppError::NotFound)?;
    Ok(Json(UserDto::from(user)))
}
```

```rust
// ✅ #[from] on Db variant — ? does the conversion; handlers stay thin
// src/routes/users.rs
use axum::{extract::{Path, State}, Json};
use crate::{db::UserRepo, error::AppError, models::UserDto};

pub async fn get_user(
    State(repo): State<UserRepo>,
    Path(id): Path<uuid::Uuid>,
) -> Result<Json<UserDto>, AppError> {
    let user = repo.find_by_id(id).await?;   // sqlx::Error → AppError::Db via #[from]
    let user = user.ok_or(AppError::NotFound)?;
    Ok(Json(UserDto::from(user)))
}
```

### 6. Validation Helper

For request body validation, map `validator::ValidationErrors` into `AppError::Validation`:

```rust
use validator::Validate;

pub fn validated<T: Validate>(val: T) -> Result<T, AppError> {
    val.validate()
        .map_err(|e| AppError::Validation(e.to_string()))?;
    Ok(val)
}
```

---

## Anti-Patterns to Block

- **Raw `String` error returns from server fns**: `Err("not found".to_string())`.
  Use `Err(AppError::NotFound)` instead.

- **`.unwrap()` or `.expect()` inside `#[server]` bodies**: panics cross the SSR/WASM
  boundary badly. Replace with `?` + an `AppError` variant.
  > **Book anchor — ch09-03 To panic! or Not to panic!:** the canonical language-level
  > discussion of when panicking is appropriate. For handler/service code that processes
  > user-supplied input, return `Result<T, AppError>` rather than panicking — the Book
  > reserves `panic!` for invariant violations and security-critical bad states, not
  > recoverable failures. See also `rust-panic-vs-result` for the full decision table.
  > https://doc.rust-lang.org/book/ch09-03-to-panic-or-not-to-panic.html

- **Leaking internal error details to client**:
  BAD: `(500, self.to_string()).into_response()`
  GOOD: log internally, return generic "internal server error" string.

- **`anyhow` in handler or domain modules**: `anyhow::Error` is not `IntoResponse`.
  Domain code must use `AppError`.

- **Per-handler `map_err` repetition**: if the same `map_err` appears more than twice,
  add a `#[from]` variant or a helper function.

- **`Box<dyn std::error::Error>` as return type**: loses type information needed for HTTP
  status mapping.

- **`unwrap_or_default()` swallowing errors silently**: this hides root causes. Use `?`
  or log and return a typed error.

---

## Forbidden Patterns

### Forbidden 1 — `.map_err(|_| ...)` Discarding the Source Error

**Forbidden:** `.map_err(|_| AppError::Internal)` or any closure that ignores the error
argument with `_`.

**Why (Book ch09-02 — Recoverable Errors with Result):** The `?` operator and `From` chain
are designed to carry source errors forward. Discarding the original error with `_` breaks
the chain; the root cause never reaches `tracing::error!`, making production debugging
impossible. Clippy's `clippy::map_err_ignore` catches this.
https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html

```rust
// ❌ Source error silently dropped — root cause is unrecoverable in logs
some_op().map_err(|_| AppError::Internal)?;
```

```rust
// ✅ Source error preserved and logged
some_op().map_err(|e| {
    tracing::error!(error = %e, "some_op failed");
    AppError::Internal
})?;
```

**Fix:** Preserve the source error as shown above, or add a `#[from]` variant that carries the source.

```bash
# Detector
grep -rn '\.map_err(|_|' src/ | grep -v '//'
```

---

### Forbidden 2 — `Box<dyn Error>` on a Public/Server-Fn Boundary

**Forbidden:** A server fn, Axum handler, or any function that crosses a public module
boundary returning `Result<T, Box<dyn std::error::Error>>` (or `Box<dyn Error + Send + Sync>`).

**Why (Book ch09-02 — Recoverable Errors with Result):** `Box<dyn Error>` erases type
information needed for HTTP status mapping. Typed errors are required for `?` chaining to
work across module boundaries: the `?` operator calls `From::from(e)`, which requires a
concrete error type. `Box<dyn Error>` is also not `IntoResponse`, so the compiler rejects it
in Axum handlers. In Leptos server fns it cannot be deserialized on the client stub.
https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html

```rust
// ❌ Erases type info — cannot map to HTTP status, cannot cross server-fn boundary
pub async fn fetch_user(id: Uuid) -> Result<UserDto, Box<dyn std::error::Error>> { ... }
```

```rust
// ✅ Typed error — maps to HTTP status via IntoResponse, works across boundaries
pub async fn fetch_user(id: Uuid) -> Result<UserDto, AppError> { ... }
```

**Fix:** Return `Result<T, AppError>`. Reserve `Box<dyn Error>` for `main.rs`/CLI entry
points where `anyhow` is already the convention.

```bash
# Detector
grep -rn 'Box<dyn.*Error' src/ | grep -v '//' | grep -v 'main\.rs\|src/bin/'
```

---

### Forbidden 3 — `map_err` Repetition Instead of `#[from]` Variant

**Forbidden:** The same `map_err(AppError::SomeVariant)` (or `map_err(|e| AppError::X(e))`)
appearing in three or more call sites.

**Why (Book ch09-02 — Recoverable Errors with Result):** The idiomatic pattern for
converting external errors is a `#[from]` variant, which auto-generates `impl From<E> for AppError`
and lets `?` do the conversion. Repeated manual `map_err` calls are maintenance debt that
`#[from]` eliminates entirely.
https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html

```rust
// ❌ Same map_err repeated at every call site
let a = op_a().map_err(AppError::Db)?;
let b = op_b().map_err(AppError::Db)?;
let c = op_c().map_err(|e| AppError::Db(e))?;
```

```rust
// ✅ #[from] generates the conversion; call sites use bare ?
#[error("db error")]
Db(#[from] sqlx::Error),
// ...
let a = op_a()?;
let b = op_b()?;
let c = op_c()?;
```

**Fix:** Add the variant with `#[from]`; replace all `.map_err(AppError::X)` with bare `?`.

```bash
# Detector (covers both the direct form and the closure form)
grep -rn -E '\.map_err\(AppError::|map_err\(\|[^|]*\|[^)]*AppError::' src/ | grep -v '//' | sort | uniq -c | sort -rn | head -20
```

---

### Forbidden 4 — `anyhow` in Domain or Handler Modules

**Forbidden:** `use anyhow` in any file other than `main.rs` or `src/bin/`.

**Why (Book ch09-02 / ch09-03):** Typed error dispatch via `AppError` is the mechanism
that enables correct HTTP status mapping and typed `?`-chaining. `anyhow::Error` does not
implement `IntoResponse` and cannot participate in the `FromServerFnError` chain. Mixing
it into domain code breaks both the HTTP layer and the server-fn layer.
https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html

```rust
// ❌ anyhow in a handler — won't compile as an Axum return type
use anyhow::{bail, Result};
pub async fn create_order(...) -> Result<Json<OrderDto>> { ... }
```

```rust
// ✅ AppError in a handler — implements IntoResponse
pub async fn create_order(...) -> Result<Json<OrderDto>, AppError> { ... }
```

**Fix:** Replace with `AppError`; move `anyhow` usage to startup/CLI boundaries.

```bash
# Detector
grep -rn 'use anyhow' src/ | grep -v 'src/main\.rs\|src/bin/'
```

---

## Verification Hooks

### Hook 1 — No unwrap/expect in server fns
```bash
# Heuristic: -A50 window may miss unwrap() in long server fns (>50 lines) and may
# flag adjacent non-server-fn code that falls within the window. Audit all matches manually.
grep -rn -A50 '#\[server\]' src/ \
  | grep -E '\.unwrap\(\)|\.expect\(' \
  | grep -v '//'
```
**Fails when**: any match is found outside a comment.
**Remediation**: replace with `?` and the appropriate `AppError` variant.

### Hook 2 — No raw String errors from server fns
```bash
# Heuristic: matches String in the error position only
grep -rn -E 'Result<[^,]+,\s*String>' src/ \
  | grep -v '//'
```
**Fails when**: server fn signatures return `Result<T, String>`.
**Remediation**: replace `String` with `AppError`.
**Caveat**: the previous `Result<.*String>` pattern over-matches success types that
contain String (e.g. `Result<Vec<String>, AppError>`). This narrower regex targets
only the error-position type.

### Hook 3 — anyhow not imported in non-main modules
```bash
grep -rn 'use anyhow' src/ | grep -v 'src/main.rs' | grep -v 'src/bin/'
```
**Fails when**: `anyhow` appears in domain or handler modules.
**Remediation**: switch to `AppError`; move `anyhow` usage to `main.rs`.

### Hook 4 — Internal errors logged before responding
```bash
# Heuristic: the canonical IntoResponse impl places tracing::error! on a separate line
# from the INTERNAL_SERVER_ERROR tuple — same-line matching produces false positives.
# Review each match: confirm a tracing::error! call appears in the same match arm.
grep -rn 'INTERNAL_SERVER_ERROR' src/ | grep -v '//'
```
**Fails when**: a 500 response is constructed without a `tracing::error!` call in the
same match arm (visually nearby in `IntoResponse`, not necessarily on the same line).
**Remediation**: add `tracing::error!(error = ?self, "internal error");` before the
500 mapping in `IntoResponse`.

---

## Related Skills

- `rust-panic-vs-result` — language-level panic-vs-Result philosophy; the definitive
  companion to this skill's ch09-03 anchor. Consult before deciding between `panic!`,
  `unwrap`, and a typed `AppError` variant.
- `rust-pattern-matching` — exhaustive `match` on error enums; the most common place
  `AppError` variants are consumed. Required when adding a new `AppError` variant.

## References

- `quality-gates` — Step 2 (clippy) will catch `unwrap_used` lint; step 7 catches
  `unwrap()` in `#[server]` bodies directly.
- `batch-error-resolution` — for handling cascading `From` / type-mismatch errors after
  adding a new `AppError` variant.
- `dto-domain` — `AppError::Validation` pairs with the domain `TryFrom` pattern.
- [thiserror crate docs](https://docs.rs/thiserror)
- [anyhow crate docs](https://docs.rs/anyhow)
- [axum `IntoResponse` docs](https://docs.rs/axum/latest/axum/response/trait.IntoResponse.html)
- [The Rust Book ch09-00 Error Handling](https://doc.rust-lang.org/book/ch09-00-error-handling.html)
- [The Rust Book ch09-02 Recoverable Errors with Result](https://doc.rust-lang.org/book/ch09-02-recoverable-errors-with-result.html)
- [The Rust Book ch09-03 To panic! or Not to panic!](https://doc.rust-lang.org/book/ch09-03-to-panic-or-not-to-panic.html)

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
