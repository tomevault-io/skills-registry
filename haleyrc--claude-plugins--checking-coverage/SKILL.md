---
name: checking-coverage
description: Guidance for checking test coverage in Go. Use when the user asks about test coverage, untested code, testing gaps, or which code needs tests or when test coverage is implicitly required for a task, even if coverage is not mentioned explicitly. Use when this capability is needed.
metadata:
  author: haleyrc
---

Before reading files to determine testing gaps, use `go test --cover` to verify test coverage.

**For a quick summary:**

```bash
go test --cover ./...             # Generate coverage for every package in the project
go test --cover ./example/lib/log # Generate coverage numbers for a specific package
go test --cover ./example/lib/... # Generate coverage numbers for a package and all its sub-packages
```

This generates lines indicating the test status, package name, test run time, and percentage of statements covered. For example:

```bash
ok      github.com/example/lib/assert   1.146s  coverage: 83.1% of statements
```

**For detailed coverage information:**

```bash
go test --coverprofile=c.out <package selector> # Generate a coverage profile
go tool cover --func=c.out                      # Output per-function coverage
rm c.out                                        # Clean up once the profile is no longer required
```

This will produce lines indicating the path to the file, the line number of the function-under-test, the name of the function, and the coverage percentage. For example:

```bash
github.com/haleyrc/lib/assert/assert.go:20:             ContentType             100.0%
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haleyrc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
