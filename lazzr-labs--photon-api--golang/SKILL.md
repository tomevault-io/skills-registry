---
name: golang
description: Writes idiomatic Go with composition, explicit errors, and focused functions. Use when writing or reviewing Go code in this project. Use when this capability is needed.
metadata:
  author: lazzr-labs
---

# Golang
* Prefer composition over inheritance (no inheritance in Go).
* Use error values, not exceptions.
* Keep functions short and focused.

## Naming

* **Types/Functions**: PascalCase (e.g. **SignInAPI**, **UserService**)
* **Variables/Fields**: camelCase (e.g. **userObj**, **input.Body**)
* **Packages**: snake_case (e.g. **auth_api**, **users_api**)

## Imports

1. Standard library
2. Third-party (e.g. **github.com/danielgtaylor/huma/v2**)
3. Internal packages (**api-go/...**)

---
> Source: [lazzr-labs/photon-api](https://github.com/lazzr-labs/photon-api) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
