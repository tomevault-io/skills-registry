---
name: apex-lego-builder
description: Scaffolds modules and components following the Apex v2 "LEGO" philosophy. Use when this capability is needed.
metadata:
  author: adelfree2023-dev
---

# 🧱 System Architecture Mapping & Modular Design (v2.0)

**Focus**: System Architecture Mapping (1).

---

## 🏗️ Architecture Protocols
- **Interdependency Mapping**: Map complex interdependencies between the SaaS platform, POS terminals, and Browser Extensions. Ensure zero circular dependencies.
- **DDD-Structured Modules**: Enforce modular design using Domain-Driven Design (DDD). Modules must contain `domain`, `application`, `infrastructure`, and `interfaces` layers.
- **Micro-Frontends (Islands)**: Scaffolds UI components as independent islands to support cross-app reuse without bloating bundles.

## 🚀 Root Solutions (Scaffolding)
- **Zero-Shot Scaffolding**: Generate full CRUD flows (Zod -> Entity -> Service -> Controller) that "snap together" with zero manual glue code required.
- **Extension-Centric Design**: Ensure all architectural components account for secure communication with browser extensions from the ground up (Requirement 12).
- **Audit-Ready Code**: Automatically inject `AuditLoggerInterceptor` and security guards into newly scaffolded modules.

## ⚖️ LEGO Rules
Modules must be self-contained. Every snap-on component must include its own unit tests and Zod validation schema.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adelfree2023-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
