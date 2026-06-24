---
name: lintroll
description: Instructional markdown with code examples that shouldn't be linted. Use when this capability is needed.
metadata:
  author: privatenumber
---
# Example Skill

Instructional markdown with code examples that shouldn't be linted.

## Usage

```js
// Semicolons and wrong indentation would normally fail
const result = doSomething();
if (result) {
  console.log(result);
}
```

```ts
// TypeScript example with style violations
interface Config {
  name: string;
  value: number;
}

const config: Config = {
  name: "test",
  value: 1,
};
```

---
> Source: [privatenumber/lintroll](https://github.com/privatenumber/lintroll) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-20 -->
