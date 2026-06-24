---
name: webauthn-attestation-hardening
description: Use when implementing or tightening attestation statement parsing, verification, and trust checks.
metadata:
  author: szijpeter
---

# WebAuthn Attestation Hardening

## Trigger

Use when touching attestation formats (`packed`, `tpm`, `android-key`, `android-safetynet`, `apple`, `none`) or trust source logic.

## Workflow

1. Expand coverage by format.
2. Add trust-path and failure-mode tests.
3. Keep optional module boundaries intact (`webauthn-attestation-mds`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/szijpeter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
