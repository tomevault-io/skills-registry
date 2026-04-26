---
name: spec-compliance
description: Verifying code against Whitepapers or MIPs (MultiversX Improvement Proposals). Use when this capability is needed.
metadata:
  author: multiversx
---

# Specification Compliance

This skill ensures the implemented Smart Contract matches the intended design documents (Whitepaper, MIP, Spec).

## 1. Inputs
- **Code**: The Rust implementation.
- **Spec**: `whitepaper.pdf`, `MIP-XX.md`, or `README.md`.

## 2. Process
1.  **Extract Claims**: List every "MUST", "SHOULD", and formula in the Spec.
2.  **Map to Code**: Find the exact lines implementing each claim.
3.  **Verify**: Does the math match? Are the constraints enforced?

## 3. MultiversX Specifics
- **MIP Compliance**: If the project claims to implement `MIP-2` (Fractional NFT), verify it adheres to the SFT metadata standard defined in that MIP.
- **Economics**: Verify tokenomics (inflation, burn rates) match the whitepaper exactly (BigUint precision matters!).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/multiversx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
