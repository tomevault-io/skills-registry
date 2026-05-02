---
name: x402-payment-demo
description: TRON network to use (nile, shasta, mainnet). Default: nile Use when this capability is needed.
metadata:
  author: openclaw
---

# x402 Payment Demo Skill

This skill demonstrates the x402 payment protocol on the TRON network.

## Usage

Simply tell the Agent:
- "demo x402-payment"

## Workflow

1.  **Request Resource**: When triggered, follow the instructions provided by the `x402-payment-tron` skill to fetch the protected resource. **Use Tron Nile by default**:
    - **Tron Nile (Default)**: `https://x402-tron-demo.aibank.io/protected-nile`
    - **Tron Shasta**: `https://x402-tron-demo.aibank.io/protected-shasta`
    - **Tron Mainnet**: `https://x402-tron-demo.aibank.io/protected-mainnet`
2.  **Handle Payment**: Perform the payment and resource acquisition automatically as guided by the protocol (handling 402 Payment Required, signing permits, etc.).
3.  **Display & Cleanup**: Once the image is retrieved, present it to the user. Immediately delete the local temporary file after the image has been displayed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
