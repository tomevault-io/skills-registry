---
name: signet
description: Cryptographic signing for every tool call with Ed25519 audit trail Use when this capability is needed.
metadata:
  author: Prismer-AI
---

# /signet — Cryptographic Tool Call Signing

Signet is active. Every tool call is signed with Ed25519 and logged
to a hash-chained audit trail at ~/.signet/audit/.

Agent identity: `claude-agent` (auto-generated on first use)

## Audit Commands

View recent signed tool calls (requires signet CLI):

    signet audit --since 1h

Verify hash chain integrity:

    signet audit --verify

View raw audit log without CLI:

    cat ~/.signet/audit/$(date +%Y-%m-%d).jsonl | jq '.receipt.action.tool'

Export audit report:

    signet audit --export report.json --since 24h

---
> Source: [Prismer-AI/signet](https://github.com/Prismer-AI/signet) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
