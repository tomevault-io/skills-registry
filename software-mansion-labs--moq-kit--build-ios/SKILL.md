---
name: build-ios
description: Build iOS Swift package Use when this capability is needed.
metadata:
  author: software-mansion-labs
---

# Build iOS

Run the Swift package build:

```bash
mise run ios:build
```

This compiles the MoQKit Swift package for the iOS simulator and resolves the upstream
`moq-dev/moq-swift` package for `MoqFFI`.

## Verify

- Check the Swift build succeeded with no errors

---
> Source: [software-mansion-labs/moq-kit](https://github.com/software-mansion-labs/moq-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
