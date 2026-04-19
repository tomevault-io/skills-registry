---
name: ops-deployment
description: Build pipeline, verification, and deployment scripts. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# Ops & Deployment

This skill guides the agent through the custom build and deployment process.

## Build System

### Build Proof
The system uses a "Build Proof" mechanism to ensure integrity.
*   **Command:** `npm run build`
*   **Trigger:** This automatically triggers `scripts/generate-build-proof.mjs`.
*   **Output:** Generates a cryptographic proof of the build artifacts.

## Infrastructure Setup

### Bootstrapping
*   **Script:** `bootstrap-ubuntu22.sh`
*   **Usage:** Run this on a fresh Ubuntu 22.04 LTS server to install dependencies (Node, Nginx, Docker, etc.) and harden the system.

### Nginx Configuration
*   **Script:** `generate-nginx-config.mjs`
*   **Purpose:** Generates a secured Nginx configuration file.
*   **Features:**
    *   Sets up reverse proxy to the Next.js app.
    *   Configures SSL/TLS settings.
    *   Applies security headers.

### Environment Setup
*   **Script:** `setup-env.js`
*   **Purpose:** Initializes environment variables securely.
*   **Security:** Checks for required secrets and prevents startup if `server-config` is invalid.

## Deployment Flow
1.  **Bootstrap:** Run `bootstrap-ubuntu22.sh` (once).
2.  **Env:** Run `node scripts/setup-env.js`.
3.  **Build:** Run `npm run build` (creates build proof).
4.  **Config:** Run `node scripts/generate-nginx-config.mjs`.
5.  **Start:** Start the application service.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
