---
name: reqwest-form-feature-required
description: | Use when this capability is needed.
metadata:
  author: strataga
---

# reqwest Form Feature Required

## Problem
When using reqwest to send form-encoded data (like OAuth token requests), the `.form()`
method may not be available, causing confusing compiler errors that suggest the method
doesn't exist.

## Context / Trigger Conditions

**Error messages you'll see:**
```
error[E0599]: no method named `form` found for struct `RequestBuilder` in the current scope
```
or
```
error[E0599]: no variant or associated item named `form` found
help: there is a method `or` with a similar name
```

**Common scenarios:**
- Making OAuth2 token refresh/exchange requests
- Submitting form data to APIs that expect `application/x-www-form-urlencoded`
- Migrating code from reqwest < 0.11 where `form` was available by default
- Using workspace dependencies where not all features are inherited

## Solution

Add the `form` feature to your reqwest dependency in `Cargo.toml`:

```toml
# Single crate
reqwest = { version = "0.13", features = ["form"] }

# With other common features
reqwest = { version = "0.13", features = ["json", "form", "rustls"] }

# In workspace Cargo.toml
[workspace.dependencies]
reqwest = { version = "0.13", default-features = false, features = ["json", "form", "rustls", "http2"] }
```

**Note:** In reqwest 0.11+, many methods are behind feature flags:
- `json` - for `.json()` method
- `form` - for `.form()` method
- `multipart` - for multipart form uploads
- `cookies` - for cookie jar support
- `stream` - for streaming request/response bodies

## Verification

After adding the feature, run `cargo check` and the error should be resolved:
```bash
cargo check
# Should compile without the "no method named form" error
```

## Example

**Before (fails to compile):**
```rust
let response = client
    .post("https://oauth2.googleapis.com/token")
    .form(&[
        ("client_id", "xxx"),
        ("client_secret", "yyy"),
        ("grant_type", "refresh_token"),
    ])
    .send()
    .await?;
```

**Fix in Cargo.toml:**
```toml
[dependencies]
reqwest = { version = "0.13", features = ["form"] }
```

## Notes

- This is a common gotcha when using `default-features = false` with reqwest
- The error message suggests `or` as an alternative method, which is misleading
- When using workspace dependencies, ensure features are properly propagated to member crates
- The `form` method serializes data as `application/x-www-form-urlencoded`
- For file uploads, use `multipart` feature and `.multipart()` method instead

## References

- [reqwest Crate Documentation](https://docs.rs/reqwest/latest/reqwest/)
- [reqwest GitHub - Feature Flags](https://github.com/seanmonstar/reqwest)
- [Rust Forum: Can't find method even though feature is in dependencies](https://users.rust-lang.org/t/cant-find-method-even-though-feature-is-in-dependencies-reqwest/80844)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/strataga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
