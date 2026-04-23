---
name: ibkr-web-client
description: Python client for the IBKR Client Portal (CP) Web API. Use when this capability is needed.
metadata:
  author: quantdiy
---

# IBKR Web Client

A lightweight Python adapter for the **Client Portal Web API**.

## Use Cases
-   **Portfolio Snapshots**: Getting full account PV (Net Liquidation Value) without TWS running.
-   **Watchlists**: Managing server-side watchlists.
-   **Authentication**: Handling CP Gateway session "tickle".

## Requirements
-   **Client Portal Gateway**: Must be running (Java process provided by IBKR).
-   **Env Vars**: `IBKR_CP_HOST` (default: localhost), `IBKR_CP_PORT` (default: 5000).

## Usage
```python
from extensions.python.ibkr_web.src.ClientPortalClient import ClientPortalClient

client = ClientPortalClient()

# Check Session
client.tickle()

# Get PnL
accounts = client.get_portfolio_accounts()
print(accounts)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/quantdiy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
