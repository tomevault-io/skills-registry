---
name: golang-packages
description: >- Use when this capability is needed.
metadata:
  author: lrstanley
---

# Go Package Discovery (pkgsite-cli)

Specialist for querying [pkg.go.dev](https://pkg.go.dev/) metadata through
[pkgsite-cli](https://github.com/golang/pkgsite/tree/master/cmd/internal/pkgsite-cli),
the reference CLI for the [pkg.go.dev v1beta API](https://pkg.go.dev/api). For
programmatic integrations, use the REST API directly; see `references/api.md`.

Introduced in the [pkg.go.dev API blog post](https://go.dev/blog/pkgsite-api).

## When to Use

- Discovering or comparing third-party Go packages not present locally
- Inspecting module versions, vulnerabilities, licenses, or README content
- Listing exported symbols, imports, or reverse dependencies for a package
- Scripting pkg.go.dev lookups with `-json` output

## When Not to Use

- Reading documentation for packages in the local module cache (use `go doc`)
- Serving docs for local or private packages (use `cmd/pkgsite` web server)
- Resolving ambiguous import paths without specifying the module

## Core Workflow

1. **Install** -- `go install golang.org/x/pkgsite/cmd/internal/pkgsite-cli@latest`
2. **Choose interface** -- CLI for terminal/scripts; REST API for application code
3. **Pick command** -- `search` for discovery; `package` or `module` for inspection
4. **Set flags** -- Request only needed data; use `-json` for machine output
5. **Handle ambiguity** -- Pass `-module` when the API reports multiple candidates

## Reference Guide

| Topic | Reference | Load When |
| --- | --- | --- |
| Package inspection | `references/package.md` | Metadata, docs, symbols, imports, imported-by, licenses |
| Module inspection | `references/module.md` | Versions, packages, vulnerabilities, README |
| Search | `references/search.md` | Finding packages or symbols on pkg.go.dev |
| REST API | `references/api.md` | Direct HTTP access, OpenAPI spec, curl, pagination |

## Commands

| Command | Purpose |
| --- | --- |
| `pkgsite-cli search <query>` | Search for packages (or symbols with `-symbol`) |
| `pkgsite-cli package <path>[@version]` | Inspect a package |
| `pkgsite-cli module <module>[@version]` | Inspect a module |
| `pkgsite-cli help` | Print usage for all commands |
| `pkgsite-cli version` | Print CLI version and Go toolchain |

Shared flags on `package`, `module`, and `search`: `-json`, `-limit` (default 25),
`-server` (default `https://pkg.go.dev`), `-timeout` (default 30s), `-x` (print URLs).
Run `pkgsite-cli <command> -h` for command-specific flags.

## Version Syntax

Append `@version` to paths for semver tags (`v1.2.3`) or `master`/`main` branches.
Omit `@version` for the latest tagged release.

## Constraints

### MUST DO

- Place flags **before** positional arguments
- Use `-module` when the API returns ambiguous path candidates
- Use `-json` in scripts; check exit code for failures
- Prefer `pkgsite-cli` or the REST API over scraping pkg.go.dev HTML

### MUST NOT DO

- Assume the CLI interface is stable (marked experimental upstream)
- Rely on the web UI's "longest module path" resolution
- Put flags after positional arguments
- Use `pkgsite-cli` instead of `go doc` on local code

## Quick Examples

```bash
pkgsite-cli search -limit 5 "structured logging"
pkgsite-cli package -symbols -imported-by github.com/google/go-cmp/cmp
pkgsite-cli module -versions -packages github.com/google/go-cmp
pkgsite-cli search -json -limit 10 uuid | jq '.items[].packagePath'
```

## Tool Comparison

| Tool | Scope |
| --- | --- |
| `go doc` | Local module cache |
| `cmd/pkgsite` | Local web server for downloaded packages |
| `pkgsite-cli` | Remote metadata: search, versions, vulns, imported-by, licenses |
| REST API | Same data as CLI; best for application integrations |

---
> Source: [lrstanley/skills](https://github.com/lrstanley/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
