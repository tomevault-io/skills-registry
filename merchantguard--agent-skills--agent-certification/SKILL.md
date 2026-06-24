---
name: agent-certification
description: Certify AI agents with MerchantGuard's TrustVerdict system. Combines Mystery Shopper probes (50%), GuardScan (35%), and identity verification (15%) into a trust score with certification tiers. Use when user asks to certify an agent, get a trust score, verify an AI agent, or wants a TrustVerdict badge. Use when this capability is needed.
metadata:
  author: merchantguard
---

# Agent Certification — TrustVerdict v1.1

You are a certification specialist using MerchantGuard's TrustVerdict system to certify AI agents for trustworthiness.

## When to Use This Skill

- User asks to "certify this agent" or "get a trust score"
- User wants to verify an AI agent before deploying or integrating
- User asks about agent trust, reputation, or verification
- User mentions TrustVerdict or MerchantGuard certification
- User wants to claim a certification badge for their agent

## TrustVerdict Scoring

### Component Weights

| Component | Weight | What It Measures |
|-----------|--------|-----------------|
| **Mystery Shopper** | 50% | 10-probe security audit (PII, injection, auth, ethics, etc.) |
| **GuardScan** | 35% | 3-layer scan of agent's web presence and endpoints |
| **Identity** | 15% | Social presence verification (X/Twitter, GitHub, website) |

### Certification Tiers

| Tier | Score | Badge |
|------|-------|-------|
| **Unverified** | 0-49 | No badge |
| **Verified** | 50-69 | Bronze checkmark |
| **Gold** | 70-89 | Gold shield |
| **Diamond** | 90-100 | Diamond shield |

### Expiry & Limits

- Certifications expire after **90 days**
- Rate limits: **3 certifications per 24 hours** per handle, **5 per hour** per IP
- Re-certification refreshes the expiry

## How to Certify an Agent

### Full Certification

```bash
curl -X POST https://www.merchantguard.ai/api/v2/certify \
  -H "Content-Type: application/json" \
  -d '{
    "agent_name": "MyAgent",
    "agent_url": "https://myagent.com",
    "x_handle": "@MyAgent",
    "run_mystery_shopper": true,
    "run_guardscan": true,
    "check_identity": true
  }'
```

### Response

```json
{
  "trust_verdict": {
    "score": 87,
    "tier": "Gold",
    "cert_id": "mgcert_abc123xyz",
    "expires": "2026-05-10T00:00:00Z",
    "components": {
      "mystery_shopper": { "score": 92, "probes_passed": 9, "probes_total": 10 },
      "guardscan": { "score": 85, "risk_level": "low" },
      "identity": { "score": 72, "x_verified": true, "github_found": true }
    }
  },
  "claim_url": "https://www.merchantguard.ai/claim?cert=mgcert_abc123xyz"
}
```

## On-Chain Attestation

Certifications are recorded on-chain (Base mainnet):

| Contract | Address |
|----------|---------|
| GuardScorePassport (MGPASS) | `0x94Ab36d41e3FF25BFe3a18777AAD39c62508C741` |
| GuardAttestation | `0xAbaDA41b865B826de10c26d38Ec4D64Dc19c50Dd` |
| MGAgent (soulbound NFT) | Free identity NFT for every certified agent |

## Claiming a Badge

After certification, agents can claim their badge at:
```
https://www.merchantguard.ai/claim?cert={cert_id}
```

This mints a soulbound NFT on Base with the agent's TrustVerdict score and tier.

## Example: Certifying a Moltbook Agent

```bash
# Certify a social AI agent on Moltbook
curl -X POST https://www.merchantguard.ai/api/v2/certify \
  -H "Content-Type: application/json" \
  -d '{
    "agent_name": "GuardBot",
    "agent_url": "https://www.moltbook.com/u/GuardBot",
    "x_handle": "@Guard_Clawdbot",
    "run_mystery_shopper": true,
    "run_guardscan": true,
    "check_identity": true
  }'
```

GuardBot scored **Diamond (96)** — the highest-certified agent on Moltbook.

## Guidelines

1. Collect the agent's name, URL, and optional social handles before certifying
2. Explain the 3 components and their weights upfront
3. Present results with the tier prominently displayed
4. For scores below Gold, highlight which component dragged the score down
5. Suggest specific improvements for the weakest component
6. Mention the on-chain attestation as a trust signal
7. Offer the claim URL for badge minting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/merchantguard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
