---
name: release-manager
description: Responsible for release planning, versioning, and deployment coordination. Use when this capability is needed.
metadata:
  author: tannergolden
---
<!-- markdownlint-disable MD041 -->

<a name="top"></a>

<div align="center">

# 📦 Release Management

**Consistency-driven. AI-native. Engineering excellence.**

[![Status: Active](https://img.shields.io/badge/Status-Active-2EA043?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![Role: Guide](https://img.shields.io/badge/Role-Guide-FE5196?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![Context: General](https://img.shields.io/badge/Context-General-9C27B0?style=for-the-badge&logo=github&labelColor=000000)](../../../../)
[![License: MIT](https://img.shields.io/badge/License-MIT-F1E05A?style=for-the-badge&labelColor=000000)](../../../../LICENSE)

</div>

---

> [!NOTE]
> This skill manages the distribution lifecycle.

## 🎯 Directives

1. **Semantic Rigor**: Strictly adhere to Semantic Versioning (SemVer).
2. **Immutable History**: Never modify a release once it is tagged.
3. **Golden Path**: Every release must pass through `Development` -> `Preview` -> `Release`.

## 🛠️ Workflows

### 1. Prepare Release

- **Trigger**: `release_prep`
- **Steps**:
  1. **Validate**: Ensure all tests and linting pass on `Development`.
  2. **Bump**: Update version in `package.json` following SemVer.
  3. **Changelog**: Update `CHANGELOG.md` with all changes since last release.
  4. **Tag**: Create a signed git tag.

## 📚 Knowledge Base

- [Releases & Versioning](/.agent/storage/docs/distribution/Releases%20%26%20Versioning.md)
- [Branching Strategy](/.agent/storage/docs/distribution/Branching%20Strategy%20%26%20Workflow.md)
- [Deployment Protocol](/.agent/storage/docs/technical/infrastructure/Deployment%20Protocols.md)
- [Workflow Rules](/.agent/storage/rules/workflows.md)

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
