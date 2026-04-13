---
name: dagger-codegen
description: | Use when this capability is needed.
metadata:
  author: dagger
---

# Dagger Codegen

## When to Load This Skill

- Editing `dagger.gen.go` or `internal/dagger/dagger.gen.go` output
- Modifying Go templates in `cmd/codegen/generator/go/templates/`
- Changing SDK interfaces in `core/sdk.go`
- Working on `dagger develop`, `dagger call`, or `dagger client install`
- Debugging why generated code looks wrong

## Critical Concepts

**"Codegen" means 4 different things in Dagger:**

| # | Name | Trigger | Key Files |
|---|------|---------|-----------|
| 1 | In-Module Bindings | `dagger develop` | `cmd/codegen/generator/go/templates/` |
| 2 | Runtime Dispatch | Module startup | `cmd/codegen/generator/go/templates/modules.go:140` |
| 3 | SDK Libraries | `go generate` | `sdk/go/generate.go` |
| 4 | Generated Clients | `dagger client install` | `_dagger.gen.go/client.go.tmpl` |

**Know which one you're dealing with before editing.**

## Key Entry Points

| To change... | Edit |
|--------------|------|
| Generated method signatures | `cmd/codegen/generator/go/templates/src/_types/object.go.tmpl` |
| Generated type definitions | `cmd/codegen/generator/go/templates/src/_types/*.go.tmpl` |
| Module `invoke()` dispatch | `cmd/codegen/generator/go/templates/modules.go:140` |
| Standalone client `Connect()` | `cmd/codegen/generator/go/templates/src/_dagger.gen.go/client.go.tmpl` |
| Template functions | `cmd/codegen/generator/go/templates/functions.go:54` |
| SDK interfaces | `core/sdk.go:20` (ClientGenerator), `:93` (CodeGenerator) |
| Built-in SDK list | `core/sdk/consts.go` |
| Python output | `sdk/python/codegen/src/codegen/generator.py` |

## Reference Files

Load based on specific need:

| Need | Load |
|------|------|
| Understanding the 4 codegen types | [codegen-types.md](references/codegen-types.md) |
| SDK architecture, interfaces, Go special case | [sdk-architecture.md](references/sdk-architecture.md) |
| Go template conditionals, two-pass generation | [go-templates.md](references/go-templates.md) |
| `dagger client install` internals | [generated-clients.md](references/generated-clients.md) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dagger) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
