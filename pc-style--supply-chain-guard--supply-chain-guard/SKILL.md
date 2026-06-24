---
name: supply-chain-guard
description: >- Use when this capability is needed.
metadata:
  author: pc-style
---

# Supply Chain Guard

This project uses [Supply Chain Guard](https://scguard.pcstyle.dev/) to review packages before they are installed.

Install this skill with:

```sh
npx skills add pc-style/supply-chain-guard --skill supply-chain-guard -y
```

Or: `scguard skill install`

## Rules for coding agents

1. **Do not** run `bun add`, `bun install`, `npm install`, `pnpm add`, `yarn add`, or `code --install-extension` unless the user explicitly bypasses scguard.
2. **Prefer** `scguard review <package[@version]>` to download and analyze without installing.
3. **Use** `scguard install <package[@version]>` when the dependency should be added after the gate passes.
4. **Assume** exit code `1` from `scguard review` means high-risk findings — do not bypass unless the user explicitly requests it.
5. **Run** `scguard doctor` when installs fail unexpectedly (missing shell hook, Socket token, or agent CLIs).

## Common commands

```sh
scguard review axios
scguard install lodash --dev
scguard scan-vsix ./extension.vsix
scguard doctor
eval "$(scguard shell-hook)"   # optional: guard normal PM commands in the shell
```

---
> Source: [pc-style/supply-chain-guard](https://github.com/pc-style/supply-chain-guard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
