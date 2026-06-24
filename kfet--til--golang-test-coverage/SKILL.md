---
name: golang-test-coverage
description: Report Go test coverage for all packages. TIL note about golang. Use when working with golang and the user mentions test coverage or related topics. Use when this capability is needed.
metadata:
  author: kfet
---

# Report Go test coverage for all packages

Write coverage to file `cover.out` and then print a report based on it
```bash
go test ./... -coverprofile cover.out
go tool cover -func cover.out
```

Same as above but a one-liner using a tmp file, which is `rm`-ed at the end
```bash
OUT=$(mktemp); go test ./... -coverprofile="$OUT" && go tool cover -func="$OUT"; rm "$OUT"
```

---
> Source: [kfet/til](https://github.com/kfet/til) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
