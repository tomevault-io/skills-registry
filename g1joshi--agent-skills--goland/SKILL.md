---
name: goland
description: GoLand Go IDE with debugging and testing. Use for Go development. Use when this capability is needed.
metadata:
  author: g1joshi
---

# GoLand

GoLand provides deep understanding of Go code. It excels at **Interface implementation** navigation and **Goroutine** debugging.

## When to Use

- **Go Microservices**: Excellent navigation between Protobuf definitions and implementations.
- **Refactoring**: Extract Interface, Embed Struct.
- **Debugging**: Visualize Goroutine stacks.

## Core Concepts

### Go Modules

Native support for `go.mod`. Syncs dependencies automatically.

### Implements

Gutter icons allow jumping to/from Interface definitions and struct implementations.

### SQL / JSON

Like all IntelliJ IDEs, it handles SQL inside string literals and JSON struct tags intelligently.

## Best Practices (2025)

**Do**:

- **Use "Fill Struct"**: Auto-complete struct fields recursively.
- **Use "Vgo" Integration**: Seamless go modules support.
- **Debug Tests**: Right click a test function -> Debug.

**Don't**:

- **Don't manually import**: Usage auto-imports packages.

## References

- [GoLand Documentation](https://www.jetbrains.com/help/go/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
