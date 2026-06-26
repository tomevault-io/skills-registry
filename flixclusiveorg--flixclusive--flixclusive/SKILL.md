---
name: android-development
description: > Use when this capability is needed.
metadata:
  author: flixclusiveorg
---

# Android Development Skill

This skill governs all Android development tasks. It is **project-agnostic** and applies to any Android codebase using Kotlin, Jetpack Compose, and modern Android architecture.

## How to Use

All rules are in the `rules/` directory alongside this file. **Read every rule file before generating or editing code.** Each file is a self-contained rule set for a specific concern.

| Rule File | When to Read |
|---|---|
| `rules/01-anti-hallucination.md` | **Always.** Read before every task. These override all other rules. |
| `rules/02-architecture.md` | When creating modules, features, or any structural code. |
| `rules/03-gradle.md` | When touching `build.gradle.kts`, adding dependencies, or creating modules. |
| `rules/04-coding-style.md` | When writing any Kotlin code (non-UI). |
| `rules/05-compose-ui.md` | When writing or editing any Jetpack Compose code. |
| `rules/06-reactive-programming.md` | When working with Flow, coroutines, or async logic. |
| `rules/07-navigation.md` | When adding screens, routes, or navigation logic. |
| `rules/08-dependency-injection.md` | When creating DI modules, injecting dependencies, or scoping. |
| `rules/09-testing.md` | When writing or editing any test code. |
| `rules/10-database.md` | When modifying Room entities, DAOs, or schemas. |
| `rules/11-naming-conventions.md` | When naming files, packages, modules, or classes. |
| `rules/12-conventional-commits.md` | When writing commit messages or generating changelogs. |

## Key Principle

> When in doubt, **read existing project code first**. Never assume. Never invent. Search, read, confirm — then generate.

---
> Source: [flixclusiveorg/Flixclusive](https://github.com/flixclusiveorg/Flixclusive) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
