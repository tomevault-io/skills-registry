---
name: proof-of-quality
description: BTC PoW grind nonce till skill/output benchmark score > threshold. Verifiable excellence for antifragile meritocracy—no hype, pure quality proof. Use for skill evaluation, fork scoring, collab verification. Use when this capability is needed.
metadata:
  author: openclaw
---

# Proof-of-Quality (PoQ)

PoW for verifiable skill quality. Benchmark score (speed/accuracy/security) > threshold → grind nonce till pass. Share PoQ JSON—recipient reruns verifies.

## Usage
node poq.js <skill_path> <threshold=95>
cron every=6h: PoQ skills.

## Workflow
1. Benchmark skill (test suite score).
2. Grind nonce till score.hash starts "0000".
3. PoQ JSON: {score, hash, nonce}.
4. Verify rerun.

Ex:
$ node poq.js skills/molt-security-auditor 95
Score: 98 >95 | PoQ: 0000a1b2... (nonce: 1234)

Prevents low-quality (meritocracy stacks!).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
