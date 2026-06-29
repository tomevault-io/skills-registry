---
name: webgpu-guide
description: Reference guide for WebGPU, WGSL, and WESL development. Use when writing graphics code, shaders, or setting up rendering pipelines. Use when this capability is needed.
metadata:
  author: fazil47
---

# WebGPU Development Guide

## Overview

This skill provides references and best practices for developing with WebGPU, WGSL, and WESL.

## References

- **WebGPU Fundamentals**: See [references/webgpu-fundamentals.md](references/webgpu-fundamentals.md) for core concepts, API structure, and initialization patterns.
- **WGSL Specification**: See [references/wgsl-spec.md](references/wgsl-spec.md) for shader language syntax, types, and built-in functions.
- **WESL Guide**: See [references/wesl-guide.md](references/wesl-guide.md) for using the WESL extension language (imports, conditional compilation).
- **wgpu (Rust)**: See [references/wgpu-rs-guide.md](references/wgpu-rs-guide.md) for the Rust implementation details.

## Usage Guidelines

When writing WebGPU code:
1.  **Check Fundamentals**: Refer to `webgpu-fundamentals.md` for proper resource usage (buffers, textures).
2.  **Verify WGSL**: Use `wgsl-spec.md` to verify built-in function signatures and type compatibility.
3.  **Leverage WESL**: If the project uses WESL, check `wesl-guide.md` for syntax.
4.  **Rust Implementation**: For Rust-specific API questions, consult `wgpu-rs-guide.md`.

## External Resources (Reference Only)

- Spec: https://www.w3.org/TR/webgpu/
- Fundamentals: https://webgpufundamentals.org/
- Best Practices: https://toji.dev/webgpu-best-practices/
- WGSL: https://www.w3.org/TR/WGSL/
- WESL: https://wesl-lang.dev/
- wgpu (Rust): https://wgpu.rs/

---
> Source: [fazil47/wgpu-renderer](https://github.com/fazil47/wgpu-renderer) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
