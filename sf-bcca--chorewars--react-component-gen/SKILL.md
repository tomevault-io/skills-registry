---
name: react-component-gen
description: Generates a new React component with a corresponding Vitest unit test file. Use this when you need to create a new UI component.
metadata:
  author: sf-bcca
---

# React Component Generator

This skill automates the creation of a React component and its test file, ensuring consistency and test coverage.

## Usage

To generate a component, run the `generate_component.cjs` script with the component name.

```bash
node .gemini/skills/react-component-gen/scripts/generate_component.cjs <ComponentName>
```

You can also specify a path relative to `components/` (e.g., `forms/TextInput`).

## What it does

1.  Creates `<ComponentName>.tsx` in `components/` (or specified subdirectory).
2.  Creates `<ComponentName>.test.tsx` in the same directory.
3.  Includes basic boilerplate code and a smoke test.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sf-bcca) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
