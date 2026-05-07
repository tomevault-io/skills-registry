---
name: tapforce-pnpm
description: Consistent practice of using pnpm as main package manager for project Use when this capability is needed.
metadata:
  author: neversight
---

# tapforce-pnpm

Guide effective and fast way to use pnpm as main package manager for project when working base on Node.js, aim to improve development, speed install packages and reduce disk space usage. Pnpm can be used to manage monorepo project or combine with higher monorepo management tool like Moon Repo (monorepo management tool).

## When to use

Use this skill when project mention or related to Tapforce, and use NodeJS as a part of coding.

## Instructions

Pnpm must be used as main nodejs package manager for project. With any nodejs shell command used in the time develop project, always replace exec part with `pnpm`.

Skill separated to rules under `./rules` with prefix, following priority:

| Priority | Rule    | prefix | Description                                         |
| -------- | ------- | ------ | --------------------------------------------------- |
| 1        | prepare | `pr`   | Steps and practices to prepare project to use pnpm. |
| 2        | policy  | `pl`   | Practices to follow when use pnpm.                  |

Each rules have own name, description, and tags.
Rule can include **Incorrect**, **Correct**, or **Example** code block.

Full version of skill and rules represent with file AGENTS.md.

## Reference

[Pnpm usage](https://pnpm.io/pnpm-cli)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
