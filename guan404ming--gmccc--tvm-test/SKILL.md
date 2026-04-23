---
name: tvm-test
description: Run TVM lint and tests. Use when this capability is needed.
metadata:
  author: guan404ming
---

# TVM Test

## Commands

| Trigger | Command |
|---|---|
| `*.py` changed | `bash docker/lint.sh -i python_format pylint` |
| `*.cc`, `*.h` changed | `bash docker/lint.sh -i clang_format cpplint` |
| `*.java`, `*_jni.cc` changed | `bash docker/lint.sh jnilint` |
| Any file | `bash docker/lint.sh asf` |
| Python tests | `pytest tests/python -xv` |

## Local Lint (without Docker)

```bash
# clang-format (check)
uv tool run clang-format --dry-run --Werror <files>

# clang-format (fix in place)
uv tool run clang-format -i <files>

# cpplint
uv run cpplint --linelength=100 <files>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guan404ming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
