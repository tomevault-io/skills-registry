---
name: tvm-ffi-test
description: Run TVM FFI tests. Use when this capability is needed.
metadata:
  author: guan404ming
---

# TVM FFI Test

## Commands

| Test | Command |
|---|---|
| C++ tests | `ctest --test-dir build --output-on-failure` |
| Python tests | `.venv/bin/pytest tests/python/ -v` |
| Specific C++ | `ctest --test-dir build -R <test_name> --output-on-failure` |
| Specific Python | `.venv/bin/pytest tests/python/<file>::<test> -v` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guan404ming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
