---
name: sc-repomix-nuget
description: > Use when this capability is needed.
metadata:
  author: randlee
---

# Generating NuGet Package Context

## Capabilities
- Compressed API surface (Tree-sitter `--compress`)
- Resolve dependencies/dependents (central registry)
- Inject frameworks/namespaces/repo URLs
- Two-tier output (API surface; docs optional in v0.4)

## Agent Delegation
| Operation              | Agent                          | Returns                                   |
|------------------------|--------------------------------|-------------------------------------------|
| Generate repomix output| `sc-repomix-nuget-generate`    | JSON: output_path, token_count, file_count|
| Resolve package graph  | `sc-repomix-nuget-validate`    | JSON: dependencies, dependents, package   |
| Assemble final context | `sc-repomix-nuget-analyze`     | JSON: output_path, token_count, sections  |

To invoke an agent: Use the Agent Runner to invoke `<agent-name>` as defined in `.claude/agents/registry.yaml`.

## Parameters
- `package_path` (default: `.`)
- `output_path` (default: `./nuget-context.xml`)
- `include_docs` (default: `false`)
- `registry_url` (optional; default documented in README)

## Flow (basic)
1) Agent Runner → `sc-repomix-nuget-validate`
2) Agent Runner → `sc-repomix-nuget-generate`
3) Agent Runner → `sc-repomix-nuget-analyze`
4) Present final `output_path`

## References
- See `output-formats.md` for the XML structure
- See `registry-schema.md` for registry details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/randlee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
