---
name: rust-conventions
description: >- Use when this capability is needed.
metadata:
  author: francisvarga
---

# Rust Conventions

## Cargo Workspace Structure

```toml
# Root Cargo.toml
[workspace]
members = ["crates/*"]

[workspace.package]
version = "0.1.0"
edition = "2021"

[workspace.dependencies]
# All shared dependencies defined here with versions
serde = { version = "1", features = ["derive"] }
tokio = { version = "1", features = ["full"] }
```

Each crate references workspace dependencies:
```toml
[dependencies]
serde = { workspace = true }
```

## Error Handling

- Library crates: `thiserror` with domain-specific error enums
- Binary crate (server): `anyhow` for top-level error propagation
- Never use `unwrap()` in library code (use `?` or `expect()` with message)
- Agent/disk load paths must return `Result<Option<T>>`, not `Option<T>` — collapsing parse errors into `None` erases diagnostic information

```rust
// Library crate pattern
#[derive(Debug, thiserror::Error)]
pub enum SegmentError {
    #[error("segment {0} not found")]
    NotFound(String),
    #[error("segment full, rotation needed")]
    Full,
    #[error("io error: {0}")]
    Io(#[from] std::io::Error),
}
```

## Error Logging

Log parse errors at `error!` level before returning `None` — silent `None` from parse failures is indistinguishable from legitimately missing resources. Instrument all fallback chain layers with structured error logging at implementation time, not during debugging.

## Serde / YAML Deserialization

- Add `#[serde(rename_all = "lowercase")]` to ALL enums deserialized from user-facing YAML/JSON — serde defaults to PascalCase, causing opaque "unknown variant" errors
- Use `#[serde(tag = "type")]` tagged union for polymorphic YAML configs (matches RuleDocument pattern)
- When serde reports `unknown variant 'X'`, immediately check for missing `#[serde(rename_all)]`

```rust
#[derive(Debug, Deserialize)]
#[serde(rename_all = "lowercase")]
pub enum ProviderKind {
    Anthropic,
    OpenAI,
    Gemini,
    Ollama,
}

#[derive(Debug, Deserialize)]
#[serde(tag = "type")]
pub enum RuleDocument {
    #[serde(rename = "anomaly")]
    Anomaly(AnomalyRule),
    #[serde(rename = "entity_schema")]
    EntitySchema(EntitySchemaConfig),
}
```

## Logging

Always use `tracing`, never `println!` or `eprintln!`:

```rust
use tracing::{info, warn, error, debug, instrument};

#[instrument(skip(data))]
pub fn ingest_batch(&self, data: &[Document]) -> Result<()> {
    info!(count = data.len(), "ingesting batch");
    // ...
}
```

## Async Patterns

- Use `tokio` for I/O-bound work (network, file system)
- Use `rayon` for CPU-bound work (algorithms, batch processing)
- Bridge with `tokio::task::spawn_blocking` for rayon in async context

```rust
let result = tokio::task::spawn_blocking(move || {
    // CPU-intensive work with rayon
    data.par_iter().map(|d| process(d)).collect()
}).await?;
```

## Trait Design

Pluggable components use traits with async methods:

```rust
#[async_trait::async_trait]
pub trait Embedder: Send + Sync {
    async fn embed(&self, texts: &[String]) -> Result<Vec<Vec<f32>>>;
    fn dimensions(&self) -> usize;
}
```

## Store Pattern

Use interior `Arc<RwLock<HashMap>>` in domain stores — AppState holds `Arc<Store>` to avoid double-locking. Follow the SessionStore file-based JSON pattern for new stores:

```rust
pub struct MyStore {
    data: Arc<RwLock<HashMap<String, MyItem>>>,
    data_dir: PathBuf,
}
```

- Use JSONL for append-heavy data (telemetry), JSON for small bounded configs (groups)

## Testing

- Always use `cargo nextest run` — never plain `cargo test`
- Place test utilities (tempfile, mockall, criterion) in `[dev-dependencies]`

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_segment_rotation_on_time_boundary() {
        // Descriptive test names
    }

    #[tokio::test]
    async fn test_api_endpoint_returns_200() {
        // Async test with tokio
    }
}
```

## Module Organization

Each crate follows:
```
crates/{name}/
├── Cargo.toml
├── src/
│   ├── lib.rs          # Public API, re-exports
│   ├── {feature}.rs    # One file per major feature
│   └── {subdir}/       # Subdirectory for related features
│       └── mod.rs
└── tests/              # Integration tests
    └── {feature}.rs
```

- Use `pub(crate)` for cross-store shared helpers — never duplicate encryption/utility code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/francisvarga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
