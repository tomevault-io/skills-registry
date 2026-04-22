---
name: apex-protocol-enforcer
description: Ensures code compliance with Apex v2 S1-S8 security protocols and Engineering Constitution. Use when this capability is needed.
metadata:
  author: adelfree2023-dev
---

# 🛡️ Security Audit & Protocol Enforcement (v2.0)

**Focus**: Security Audit (7).

---

## 🏁 Real-time Security Audit
- **Vulnerability Detection**: Real-time detection of SQLi, XSS, and PII leaks. Utilize strictly typed Drizzle templates to prevent injection.
- **Audit Logging (S4)**: Mandatory auditing of all state-changing operations via NestJS Interceptors and `audit_logs` table.
- **PII Protection (S7)**: Enforce AES-256-GCM encryption for all sensitive data at rest using `packages/encryption`.

## 🚀 Root Solutions (Security)
- **Zero-Trust Access**: Strictly enforce `TenantScopedGuard` and `AuthGuard` on all non-public controller methods.
- **Header Hardening (S8)**: Mandatory implementation of Helmet, CSP, and HSTS headers.
- **Validation Gates (S3)**: Global execution of `ZodValidationPipe`. Manual DTOs without Zod schemas are a protocol breach.

## ⚖️ Engineering Constitution
- **Lego Philosophy**: Strict modular isolation using DDD (Domain-Driven Design).
- **Zod as Truth**: All types must derive from Zod schemas.
- **Monorepo Strategy**: `apps/*` must never import from another `apps/*`. Cross-app communication via shared packages only.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adelfree2023-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
