---
name: apex-cloud-native-orchestrator
description: Manages complex multi-tenant infrastructure, including subdomain routing, SSL automation, and Docker build performance optimizations.
metadata:
  author: adelfree2023-dev
---

# 🌩️ Cloud-Native Deployment & CI/CD (v2.0)

**Focus**: Cloud-Native Deployment (15), Cross-Platform Integration (12), & CI/CD Pipeline Scripting (19).

---

## 🏗️ Cloud-Native Protocols
- **Expert-Level Containerization**: Mandatory multi-stage Docker builds. Use Bun's native performance and minimal image footprints.
- **Docker Layer Optimization**: Strictly order commands to leverage build caching (S3 Build Performance).
- **Turbo Build Strategy**: Minimize downtime during emergency remediations. Use targeted container rebuilds (`docker-compose up -d --build <service>`) and incremental layer synchronization to avoid system-wide 'Scorched Earth' resets unless necessary.
- **Subdomain Orchestration**: Implement dynamic Traefik ingress for SSL/Wildcard support (*.duckdns.org).

## 🚀 Root Solutions (Deployment)
- **CI/CD Quality Gates**: Automated deployment flows with mandatory health check gates (Requirement 19). No deployment proceeds without total test pass.
- **Cross-Platform Communication**: Secure, bit-for-bit logical synchronization between Browser Extensions and Backend using encrypted streams (Requirement 12).
- **Environment Parity**: Mandatory 1:1 mirroring between Dev and Prod configurations to eliminate "Works on my machine" syndrome.

## ⚖️ Operational Rule
All infrastructure changes must be idempotent. Deployment scripts must handle rollbacks automatically on service health failure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adelfree2023-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
