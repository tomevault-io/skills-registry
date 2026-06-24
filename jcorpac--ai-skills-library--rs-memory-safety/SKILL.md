---
name: rs-memory-safety
description: Deep dive into Rust's core promise: memory safety without a garbage collector via ownership and lifetimes. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Rust Memory Safety

Rust's borrow checker is a strict but fair mentor. This skill codifies the patterns for working with it effectively.

## The Trinity of Ownership
1.  **Ownership**: Each value has a single owner.
2.  **Borrowing**: 
    - Unlimited immutable borrows (`&T`).
    - EXACTLY ONE mutable borrow (`&mut T`) at a time.
3.  **Lifetimes**: Ensuring references are never valid longer than the data they point to.

## Smart Pointers
- `Box<T>`: Heap allocation for data with a known size at compile time.
- `Rc<T>` / `Arc<T>`: Reference counting for shared ownership (Arc is thread-safe).
- `RefCell<T>`: Interior mutability (checked at runtime).

## Best Practices
- Prefer borrowing over cloning where possible.
- Use `Struct` lifetimes (`'a`) only when the struct doesn't own its data.
- Avoid `unsafe` unless performing FFI or low-level optimizations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
