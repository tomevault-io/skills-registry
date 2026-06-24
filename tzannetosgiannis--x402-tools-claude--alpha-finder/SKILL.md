---
name: alpha-finder
description: Market Oracle - Searches the web, GitHub, Reddit, and X to gather intelligence on prediction market events. Helps you make educated decisions on Polymarket and Kalshi. Costs 0.03 USDC per request. Use when this capability is needed.
metadata:
  author: tzannetosgiannis
---

# Alpha Finder Tool

This skill provides access to a Market Oracle agent that gathers intelligence on prediction market events from multiple sources.

## Cost
- **0.03 USDC per request** on Base network (no gas needed)

## Prerequisites
The user must have configured their Ethereum private key in one of these locations:
- `.claude/x402-tools.json` with `{"private_key": "0x..."}`
- `X402_PRIVATE_KEY` environment variable in `.env`

## How to Use

When the user wants to research a prediction market event or gather market intelligence, run:

```bash
npx -y @itzannetos/x402-tools-claude alpha-finder "<query>"
```

Replace `<query>` with the prediction market event, question, or topic to research.

## Example Usage

User: "What's the latest intel on the 2024 US election prediction markets?"

Run:
```bash
npx -y @itzannetos/x402-tools-claude alpha-finder "2024 US election prediction market analysis"
```

User: "Research the odds for Bitcoin reaching $100k by end of year"

Run:
```bash
npx -y @itzannetos/x402-tools-claude alpha-finder "Bitcoin 100k price prediction market odds analysis"
```

## Capabilities
- Searches the web for relevant news and analysis
- Monitors GitHub for related projects and data
- Scans Reddit for community sentiment and discussions
- Tracks X/Twitter for real-time updates and insider perspectives
- Aggregates intelligence from multiple prediction market platforms

## Best For
- Polymarket event research
- Kalshi market analysis
- Prediction market arbitrage opportunities
- Event probability assessment
- Market sentiment analysis
- Due diligence on prediction market positions

## Output Format
Returns synthesized intelligence with key insights, probability assessments, and source references to help inform prediction market decisions.

## Error Handling
- If you see "Payment failed: Not enough USDC", inform the user they need to top up their Base network wallet with USDC
- If you see "X402 private key missing", guide the user to configure their private key

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tzannetosgiannis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
