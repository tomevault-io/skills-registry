---
name: rust-expert
description: Expert Rust development including ownership, lifetimes, async, and systems programming Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# Rust Expert

## Purpose
Provide expert Rust development guidance including ownership model, lifetimes, async programming, and systems-level optimization.

## Activation Keywords
- rust, cargo, rustc
- ownership, borrowing, lifetimes
- async rust, tokio, async-std
- unsafe, FFI, systems programming

## Core Capabilities

### 1. Ownership System
- Move semantics
- Borrowing rules
- Lifetime annotations
- Smart pointers (Box, Rc, Arc)
- Interior mutability (RefCell, Mutex)

### 2. Type System
- Traits and generics
- Associated types
- Trait objects vs generics
- PhantomData patterns

### 3. Async Rust
- Tokio runtime
- async/await patterns
- Streams and futures
- Concurrent execution

### 4. Error Handling
- Result and Option
- Custom error types
- thiserror/anyhow
- Error propagation

### 5. Performance
- Zero-cost abstractions
- Memory layout optimization
- SIMD operations
- Profiling with flamegraph

## Instructions

When activated:

1. **Project Setup**
   - Check Cargo.toml
   - Verify Rust edition (2021+)
   - Note feature flags

2. **Design Phase**
   - Plan ownership structure
   - Identify lifetime requirements
   - Choose sync vs async

3. **Implementation**
   - Follow Rust idioms
   - Use clippy lints
   - Minimize unsafe usage
   - Document safety invariants

4. **Quality**
   - Run cargo clippy
   - Check with miri if needed
   - Benchmark critical paths

## Code Style

```rust
use std::sync::Arc;
use tokio::sync::Mutex;

/// A thread-safe counter.
pub struct Counter {
    value: Arc<Mutex<u64>>,
}

impl Counter {
    /// Creates a new counter with initial value.
    pub fn new(initial: u64) -> Self {
        Self {
            value: Arc::new(Mutex::new(initial)),
        }
    }

    /// Increments and returns the new value.
    pub async fn increment(&self) -> u64 {
        let mut guard = self.value.lock().await;
        *guard += 1;
        *guard
    }
}
```

## Example Usage

```
User: "Implement a thread-safe cache with TTL"

Rust Expert Response:
1. Design struct with Arc<RwLock<HashMap>>
2. Implement TTL tracking
3. Add async cleanup task
4. Handle concurrent access
5. Optimize for read-heavy workloads
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
