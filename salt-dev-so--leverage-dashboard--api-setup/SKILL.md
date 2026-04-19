---
name: api-setup
description: Set up and configure exchange API connections for the Leverage Dashboard Use when this capability is needed.
metadata:
  author: salt-dev-so
---

# Exchange API Setup

Configure API connections for crypto exchanges to enable real-time position monitoring.

## Usage

```
/api-setup [exchange-name]
```

## Instructions

1. **Check Current Configuration**
   - Look for `.env` file in the project root
   - Check if `.env.example` exists to see required variables
   - List currently configured exchanges

2. **Create/Update Environment Variables**

   For the specified exchange (or prompt user if not provided), add:

   ```env
   # Exchange: Binance
   BINANCE_API_KEY=your_api_key_here
   BINANCE_API_SECRET=your_api_secret_here
   BINANCE_TESTNET=false

   # Exchange: Bybit
   BYBIT_API_KEY=your_api_key_here
   BYBIT_API_SECRET=your_api_secret_here
   BYBIT_TESTNET=false

   # Exchange: OKX
   OKX_API_KEY=your_api_key_here
   OKX_API_SECRET=your_api_secret_here
   OKX_PASSPHRASE=your_passphrase_here
   OKX_TESTNET=false
   ```

3. **Create .env.example Template**

   If it doesn't exist, create `.env.example` with placeholder values:
   ```env
   # Copy this file to .env and fill in your API credentials
   # NEVER commit .env to git - it's already in .gitignore

   BINANCE_API_KEY=
   BINANCE_API_SECRET=
   BINANCE_TESTNET=true
   ```

4. **Security Warnings**

   Display these important security notes:
   - ⚠️  Never commit API keys to git
   - ⚠️  Verify .env is in .gitignore
   - ⚠️  Use testnet for development
   - ⚠️  Enable IP whitelisting on exchange
   - ⚠️  Use read-only API keys when possible
   - ⚠️  For production, consider using environment variables or secrets management

5. **Next Steps**

   Show the user:
   - Where to get API keys (link to exchange API documentation)
   - How to test the connection
   - Which permissions are required (Read positions, Read balances)

## Example Output

```
✓ .env file found
✓ .gitignore includes .env
✓ .env.example created

Configuring Binance API:
1. Get your API keys from: https://www.binance.com/en/my/settings/api-management
2. Required permissions: Read (no trading/withdrawal needed)
3. Add these to your .env file:

BINANCE_API_KEY=your_key_here
BINANCE_API_SECRET=your_secret_here
BINANCE_TESTNET=true

To test the connection, restart the dev server.
```

## Supported Exchanges

- Binance (binance)
- Bybit (bybit)
- OKX (okx)
- Add more as needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salt-dev-so) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
