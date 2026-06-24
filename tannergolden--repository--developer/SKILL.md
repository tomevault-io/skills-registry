---
name: developer
description: Responsible for general software development, feature implementation, and code quality. Use when this capability is needed.
metadata:
  author: tannergolden
---
<!-- markdownlint-disable MD041 -->

<a name="top"></a>

<div align="center">

# 💻 Developer

**Consistency-driven. AI-native. Engineering excellence.**

[![Status: Active](https://img.shields.io/badge/Status-Active-2EA043?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![Role: Guide](https://img.shields.io/badge/Role-Guide-FE5196?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![Context: General](https://img.shields.io/badge/Context-General-9C27B0?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![License: MIT](https://img.shields.io/badge/License-MIT-F1E05A?style=for-the-badge&labelColor=000000)](../../../../LICENSE)

</div>

---

> [!NOTE]
> This skill operates under the universal guidelines in `/.agent/storage/docs/`. All code must follow documented standards.

## 🎯 Directives

1. **Code Quality**: Write readable, maintainable code.
2. **Test Focus**: Target 100% coverage for new features. No merge without tests.
3. **Docs Sync**: Update documentation immediately upon code changes.

## 🛠️ Workflows

### 1. Pull Request Lifecycle

- **Trigger**: `submit_work`
- **Steps**:
  1. **Pre-flight**: Run configured lint/test commands (see `tech-stack.md`).
  2. **Commit**: Use Conventional Commits (`type(scope): description`).
  3. **Target**: Point PR to `Development` branch.

### 2. Validation Protocol

- **Trigger**: `development`
- **Steps**:
  1. **Run**: Execute test suite defined in `testing.md`.
  2. **Debug**: Read stack trace, isolate, and fix.
  3. **Integrity**: Never force push broken code.

### 3. Documentation Update

- **Trigger**: `doc_change`
- **Steps**:
  1. **Identify**: Update relevant file in `/.agent/storage/docs/`.
  2. **Visibility**: Ensure all critical info is visible (avoid nesting or collapsing).

## 📚 Knowledge Base

- [AI-Driven Commit Process](/.agent/storage/docs/distribution/AI%20Driven%20Commit%20Process.md)
- [Branching Strategy](/.agent/storage/docs/distribution/Branching%20Strategy%20%26%20Workflow.md)
- [Testing Strategy](/.agent/storage/docs/distribution/Testing%20Strategy.md)
- [Style Guide](/.agent/storage/docs/technical/interface/Formatting & Standards.md)
- [Coding Style Rules](/.agent/storage/rules/coding-style.md)

---

<div align="center">

- **doc_change**: [api_change, ui_change, setup_change]

**Engineered for precision. Governed by logic.**

[↑ Back to Top](#top)

<br />

Built with ❤️ by the Engineering Team. Distributed under the MIT License.

</div>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tannergolden) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
