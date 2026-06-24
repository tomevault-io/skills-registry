---
name: recipe-subaccount-capital-rotation
description: Rotate capital between subaccounts based on strategy performance. Use when this capability is needed.
metadata:
  author: krakenfx
---

# Subaccount Capital Rotation

> **PREREQUISITE:** Load the following skills to execute this recipe: `kraken-subaccount-ops`, `kraken-portfolio-intel`

Evaluate strategy performance per subaccount and reallocate capital to winning strategies.

> **CAUTION:** Transfers move real funds. Always confirm IIBANs and amounts.

## Steps

1. Check main account balance: `kraken balance -o json 2>/dev/null`
2. Check each subaccount balance (via their API keys or main account view)
3. Review trade history per subaccount to calculate P&L
4. Rank strategies by risk-adjusted return
5. Calculate new allocation (increase capital for outperformers, reduce for underperformers)
6. Obtain IIBANs from account settings or the Kraken web UI (IIBANs are account-specific identifiers like `ABCD 1234 EFGH 5678`)
7. Transfer from underperforming subaccount to main (requires human approval): `kraken subaccount transfer USD 2000 --from $SUB_IIBAN --to $MAIN_IIBAN -o json 2>/dev/null`
8. Transfer from main to outperforming subaccount: `kraken subaccount transfer USD 2000 --from $MAIN_IIBAN --to $SUB_IIBAN -o json 2>/dev/null`
9. Verify balances after transfers: `kraken balance -o json 2>/dev/null`

---
> Source: [krakenfx/kraken-cli](https://github.com/krakenfx/kraken-cli) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
