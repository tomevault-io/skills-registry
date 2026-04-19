---
name: python-plugin-wrapper
description: python plugin wrapper Use when this capability is needed.
metadata:
  author: kreuzberg-dev
---

Wrap Python plugins with Rust FFI

1. Create wrapper struct holding Py<PyAny>
2. Cache frequently-accessed data:
   - name
   - version
   - supported languages
3. Implement initialize():
   - Acquire GIL with Python::attach()
   - Call Python __init__
   - Cache result
4. Implement main async method:
   a. Clone Python object reference
   b. Spawn blocking task
   c. Acquire GIL inside task
   d. Call Python method
   e. Translate Python types to Rust
   f. Handle PyException
   g. Return Rust result
5. Release resources in shutdown()

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kreuzberg-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
