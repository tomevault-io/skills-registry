---
name: rust-performance
description: Master Rust performance - profiling, benchmarking, and optimization Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Rust Performance Skill

Master performance optimization: profiling, benchmarking, and zero-cost abstractions.

## Quick Start

### Benchmarking

```toml
[dev-dependencies]
criterion = { version = "0.5", features = ["html_reports"] }

[[bench]]
name = "my_bench"
harness = false
```

```rust
use criterion::{criterion_group, criterion_main, Criterion, black_box};

fn benchmark(c: &mut Criterion) {
    c.bench_function("fib", |b| {
        b.iter(|| fibonacci(black_box(20)))
    });
}

criterion_group!(benches, benchmark);
criterion_main!(benches);
```

### Profiling

```bash
cargo install flamegraph
cargo flamegraph --bin my-app
```

## Optimization Patterns

### Avoid Allocations

```rust
// ❌ Allocates each iteration
for s in strings {
    result = result + &s;
}

// ✅ Pre-allocate
let mut result = String::with_capacity(total_len);
for s in strings {
    result.push_str(&s);
}
```

### Use Iterators

```rust
// ❌ Intermediate collection
let v: Vec<_> = data.iter().map(|x| x * 2).collect();
let sum: i32 = v.iter().sum();

// ✅ Lazy chain
let sum: i32 = data.iter().map(|x| x * 2).sum();
```

### Cow for Optional Clone

```rust
use std::borrow::Cow;

fn process(input: &str) -> Cow<str> {
    if input.contains("bad") {
        Cow::Owned(input.replace("bad", "good"))
    } else {
        Cow::Borrowed(input)
    }
}
```

## Release Profile

```toml
[profile.release]
lto = true
codegen-units = 1
opt-level = 3
```

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Slow debug | Use `--release` |
| Memory spikes | Use streaming |
| Cache misses | Improve data layout |

## Resources

- [Rust Performance Book](https://nnethercote.github.io/perf-book/)
- [Criterion.rs](https://bheisler.github.io/criterion.rs/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
