---
name: find-people
description: OSINT agent for researching individuals and professional entities. Retrieves career timelines, professional backgrounds, and biographical data. Costs 0.15 USDC per request. Use when this capability is needed.
metadata:
  author: tzannetosgiannis
---

# Find People Tool

This skill provides access to a real-time Open Source Intelligence (OSINT) agent specialized in researching individuals and professional entities.

## Cost
- **0.15 USDC per request** on Base network (no gas needed)

## Prerequisites
The user must have configured their Ethereum private key in one of these locations:
- `.claude/x402-tools.json` with `{"private_key": "0x..."}`
- `X402_PRIVATE_KEY` environment variable in `.env`

## How to Use

When the user wants to research a person or professional entity, run:

```bash
npx -y @itzannetos/x402-tools-claude find-people "<query>"
```

Replace `<query>` with the person's name, role, company, or other identifying information.

## Example Usage

User: "Find information about Vitalik Buterin"

Run:
```bash
npx -y @itzannetos/x402-tools-claude find-people "Vitalik Buterin Ethereum founder"
```

## Capabilities
- Identifies people by name, role, or company affiliation
- Retrieves verified career timelines and professional backgrounds
- Finds similar professionals in any industry or domain
- Synthesizes biographical information with source citations
- Validates identities across LinkedIn, company sites, and public records

## Best For
- Due diligence research on potential hires or partners
- Competitive intelligence on industry leaders
- Journalist & researcher background verification
- Sales prospecting and lead enrichment
- Investor research on startup founders and executives

## Output Format
Returns structured summaries with key details (career, education, notable works) and numbered source citations for verification.

## Error Handling
- If you see "Payment failed: Not enough USDC", inform the user they need to top up their Base network wallet with USDC
- If you see "X402 private key missing", guide the user to configure their private key

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tzannetosgiannis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
