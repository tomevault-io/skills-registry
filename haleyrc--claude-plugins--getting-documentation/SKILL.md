---
name: getting-documentation
description: Guidance for obtaining documentation in Go. Use when requiring documentation on Go entities e.g. packages, modules, types, functions, methods, etc. Use when this capability is needed.
metadata:
  author: haleyrc
---

Use the `go doc` CLI for looking up documentation.

**For items in the standard library**, you can reference packages and their contents directly:

```bash
go doc http              # Returns documentation for the http package
go doc http.Server       # Returns documentation for the Server type
go doc http.Server.Close # Returns documentation for the Close method
```

**For third-party code**, the package you are querying must already exist in `go.mod` or you will see a "cannot find package error". You must also include the full module path when calling `go doc`:

```bash
go doc github.com/google/uuid            # Returns documentation for the uuid package
go doc github.com/google/uuid.UUID       # Returns documentation for the UUID type
go doc github.com/google/uui.UUID.String # Returns documentation for the String method
```

**For local code (code in the current project repository)**, you can either include the full module path _or_ a relative path:

```bash
# Assuming we are in the `example` project:
go doc github.com/haleyrc/example/lib/log
go doc ./example/lib/log
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haleyrc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
