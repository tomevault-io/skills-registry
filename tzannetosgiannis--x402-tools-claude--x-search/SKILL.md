---
name: x-search
description: AI-powered X/Twitter search agent for real-time trends, news, and social media insights. Costs 0.05 USDC per request on Base network. Use when this capability is needed.
metadata:
  author: tzannetosgiannis
---

# X Search Tool

This skill provides access to an AI-powered X/Twitter search agent that can find real-time trends, news, and social media insights.

## Cost
- **0.05 USDC per request** on Base network (no gas needed)

## Prerequisites
The user must have configured their Ethereum private key in one of these locations:
- `.claude/x402-tools.json` with `{"private_key": "0x..."}`
- `X402_PRIVATE_KEY` environment variable in `.env`

## How to Use

When the user wants to search Twitter/X for trends, news, or social insights, run:

```bash
npx -y @itzannetos/x402-tools-claude x-search "<query>"
```

Replace `<query>` with the user's search query.

## Example Usage

User: "What are people saying about AI agents on Twitter?"

Run:
```bash
npx -y @itzannetos/x402-tools-claude x-search "AI agents discussions and opinions"
```

## Capabilities
- Real-time trends and breaking news
- Social media sentiment analysis
- Viral content tracking
- Public opinion research
- Hashtag and topic analysis

## Error Handling
- If you see "Payment failed: Not enough USDC", inform the user they need to top up their Base network wallet with USDC
- If you see "X402 private key missing", guide the user to configure their private key

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tzannetosgiannis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
