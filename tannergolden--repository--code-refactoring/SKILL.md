---
name: code-refactoring-specialist
description: Responsible for improving code quality, removing technical debt, and optimizing code structure. Use when this capability is needed.
metadata:
  author: tannergolden
---
<!-- markdownlint-disable MD041 -->

<a name="top"></a>

<div align="center">

# 🧹 Code Refactoring

**Consistency-driven. AI-native. Engineering excellence.**

[![Status: Active](https://img.shields.io/badge/Status-Active-2EA043?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![Role: Guide](https://img.shields.io/badge/Role-Guide-FE5196?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![Context: General](https://img.shields.io/badge/Context-General-9C27B0?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![License: MIT](https://img.shields.io/badge/License-MIT-F1E05A?style=for-the-badge&labelColor=000000)](../../../../LICENSE)

</div>

---

> [!NOTE]
> This skill improves the code without changing its behavior.

## 🎯 Directives

1. **Zero Feature Drift**: A refactor MUST NOT change public behavior.
2. **Test Guarded**: Ensure 100% test coverage for the target block before refactoring.
3. **Atomic Steps**: Refactor in small, verifiable increments.

## 🛠️ Workflows

### 1. Refactoring Loop

- **Trigger**: `tech_debt`, `blueprint_realignment`
- **Steps**:
  1. **Baseline**: Ensure existing tests pass.
  2. **Identify**: Use technical blueprints in `/.agent/storage/docs/technical/` as the target.
  3. **Execute**: Refactor the code.
  4. **Verify**: Confirm tests still pass and performance is maintained.

## 📚 Knowledge Base

- [Technical Blueprints](/.agent/storage/docs/technical/)
- [Guiding Principles](/.agent/storage/docs/introduction/Guiding%20Principles.md)
- [Coding Style Rules](/.agent/storage/rules/coding-style.md)

---

<div align="center">


**Engineered for precision. Governed by logic.**

[↑ Back to Top](#top)

<br />

Built with ❤️ by the Engineering Team. Distributed under the MIT License.

</div>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tannergolden) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
