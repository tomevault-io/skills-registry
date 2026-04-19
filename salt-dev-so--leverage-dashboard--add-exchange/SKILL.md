---
name: add-exchange
description: Add support for a new cryptocurrency exchange to the dashboard Use when this capability is needed.
metadata:
  author: salt-dev-so
---

# Add Exchange Support

Add integration for a new cryptocurrency exchange to enable position monitoring.

## Task

Add support for the exchange specified in `$ARGUMENTS`. If no exchange is specified, ask the user which exchange to add.

## Steps

1. **Research the Exchange**
   - Look up the exchange's official API documentation
   - Identify the REST API endpoints for:
     - Fetching positions
     - Getting account balance
     - Retrieving liquidation prices
   - Check if they have a TypeScript/JavaScript SDK
   - Note authentication requirements (API key, secret, passphrase, etc.)

2. **Update Type Definitions**

   Add the exchange to relevant types in `src/types/index.ts`:
   - Update `exchange` field to include the new exchange name
   - Add exchange-specific configuration if needed

3. **Create Exchange Service**

   Create `src/services/exchanges/{exchange-name}.ts`:
   ```typescript
   import { Position } from '../../types';

   export interface {ExchangeName}Config {
     apiKey: string;
     apiSecret: string;
     testnet: boolean;
   }

   export class {ExchangeName}Service {
     constructor(config: {ExchangeName}Config) {}

     async getPositions(): Promise<Position[]> {}
     async getAccountBalance(): Promise<number> {}
   }
   ```

4. **Add Environment Variables**

   Update `.env.example`:
   ```env
   # {Exchange Name}
   {EXCHANGE}_API_KEY=
   {EXCHANGE}_API_SECRET=
   {EXCHANGE}_TESTNET=true
   ```

5. **Update Mock Data**

   Add the exchange to `src/services/mockData.ts`:
   ```typescript
   const exchanges = ['Binance', 'Bybit', 'OKX', '{NewExchange}'];
   ```

6. **Documentation**

   Add exchange-specific notes to README.md:
   - API documentation link
   - Required permissions
   - Special configuration steps
   - Known limitations

7. **Test Integration**

   Create a test file or add test cases:
   - Mock API responses
   - Test position parsing
   - Test error handling
   - Verify liquidation price calculations

## Example Implementation

For adding Kraken exchange:

```typescript
// src/services/exchanges/kraken.ts
import { Position } from '../../types';

export interface KrakenConfig {
  apiKey: string;
  apiSecret: string;
}

export class KrakenService {
  private baseUrl = 'https://api.kraken.com';

  constructor(private config: KrakenConfig) {}

  async getPositions(): Promise<Position[]> {
    // Implementation
    const response = await fetch(`${this.baseUrl}/0/private/OpenPositions`, {
      method: 'POST',
      headers: this.getHeaders(),
    });

    const data = await response.json();
    return this.parsePositions(data);
  }

  private parsePositions(data: any): Position[] {
    // Parse Kraken API response to Position[]
  }

  private getHeaders() {
    // Generate authentication headers
  }
}
```

## Common Exchange APIs

- **Binance**: https://binance-docs.github.io/apidocs/spot/en/
- **Bybit**: https://bybit-exchange.github.io/docs/v5/intro
- **OKX**: https://www.okx.com/docs-v5/en/
- **Kraken**: https://docs.kraken.com/rest/
- **Bitget**: https://www.bitget.com/api-doc/
- **Gate.io**: https://www.gate.io/docs/developers/apiv4/en/

## Notes

- Always use testnet/sandbox for initial testing
- Handle rate limiting (most exchanges limit to 1200 requests/minute)
- Parse dates correctly (Unix timestamp vs ISO string)
- Convert exchange-specific terminology to our Position interface
- Test with multiple position types (long/short, different symbols)
- Handle exchange-specific edge cases (isolated vs cross margin)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salt-dev-so) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
