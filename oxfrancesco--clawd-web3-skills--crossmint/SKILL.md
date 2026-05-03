---
name: crossmint-wallets
description: Create and manage blockchain wallets for users, agents, and companies using Crossmint API. Supports EVM chains, Solana, and Stellar. Use when this capability is needed.
metadata:
  author: oxfrancesco
---

# Crossmint Wallet Management

Create programmable wallets for users, AI agents, and treasury operations using the Crossmint API.

## Setup

Requires API key in Doppler. The scripts will check for `WALLET_API_KEY` first (from your Doppler `mao-mao/prd` config), then fall back to `CROSSMINT_API_KEY`.

Get your API key from [Crossmint Console](https://staging.crossmint.com/console) and add it to Doppler:

```bash
doppler secrets set WALLET_API_KEY sk_staging_... --project mao-mao --config prd
```

## Scripts

All scripts use Bun runtime. Each script asks for confirmation before executing API calls. Use `-y` or `--yes` to skip confirmation (for automation).

### 1. Create Wallet

Create a new smart wallet for an agent or user.

```bash
# Using Doppler (recommended - picks up WALLET_API_KEY automatically)
doppler run --project mao-mao --config prd --command 'bun scripts/create-wallet.ts'

# Create wallet with custom user ID
doppler run --project mao-mao --config prd --command 'bun scripts/create-wallet.ts --userId AGENT01'

# Create non-custodial wallet (for production)
doppler run --project mao-mao --config prd --command 'bun scripts/create-wallet.ts --signer evm-passkey-signer'

# Specify chain
doppler run --project mao-mao --config prd --command 'bun scripts/create-wallet.ts --chain base-sepolia'

# Skip confirmation prompt (for automation)
doppler run --project mao-mao --config prd --command 'bun scripts/create-wallet.ts --userId AGENT01 --yes'
```

### 2. Get Wallet

Retrieve wallet details by address or user ID.

```bash
doppler run --project mao-mao --config prd --command 'bun scripts/get-wallet.ts --address 0x1234...'
doppler run --project mao-mao --config prd --command 'bun scripts/get-wallet.ts --userId AGENT01'
```

### 3. Fund Wallet (Testnet)

Fund a testnet wallet with USDC for testing.

```bash
doppler run --project mao-mao --config prd --command 'bun scripts/fund-wallet.ts --address 0x1234... --amount 100'
```

### 4. Transfer Tokens

Transfer tokens from one wallet to another.

```bash
# Basic transfer (default: base-sepolia, USDC)
doppler run --project mao-mao --config prd --command 'bun scripts/transfer.ts --from 0x1234... --to 0x5678... --amount 10 --token usdc'

# ETH transfer on Sepolia (note: use "ethereum-sepolia" not "sepolia")
doppler run --project mao-mao --config prd --command 'bun scripts/transfer.ts --from 0x1234... --to 0x5678... --amount 0.01 --token eth --chain ethereum-sepolia --yes'
```

**Important:** Both wallets must be Crossmint-managed for transfers to work. Check with `get-wallet.ts` first.

## Wallet Types

| Type | Description | Use Case |
|------|-------------|----------|
| `evm-smart-wallet` | EVM-compatible smart wallet | Agents, users on Base, Polygon, etc. |
| `solana-custodial-wallet` | Solana custodial wallet | Solana-based agents |

## Signer Types

| Signer | Custody | Use Case |
|--------|---------|----------|
| `evm-fireblocks-custodial` | Custodial | Development/testing |
| `evm-passkey-signer` | Non-custodial | Production apps |
| `evm-keypair-signer` | Non-custodial | Server-side agents |

## Supported Chains

**Important:** Use full chain names in API calls, not short names:

| Common Name | API Chain Name |
|-------------|----------------|
| Sepolia | `ethereum-sepolia` |
| Base Sepolia | `base-sepolia` |
| Ethereum | `ethereum` |
| Base | `base` |
| Polygon | `polygon` |

**Full list:** EVM chains (ethereum, base, polygon, arbitrum, optimism, avalanche) + testnets (-sepolia suffix), plus solana and stellar.

**Gotcha:** `--chain sepolia` will fail. Use `--chain ethereum-sepolia`.

## Environment Variables

The scripts automatically use your Doppler secrets. Configured in `mao-mao/prd`:

```env
WALLET_API_KEY=sk_staging_...       # Your Crossmint API key (from Doppler)
CROSSMINT_ENV=staging               # "staging" or "www" for production (optional)
```

**Note:** Scripts check for `WALLET_API_KEY` first (Doppler), then `CROSSMINT_API_KEY` (fallback).

## Testing

Run unit tests:

```bash
bun test
```

Run E2E test (requires `CROSSMINT_API_KEY`):

```bash
bun tests/e2e.test.ts
```

The E2E test will:
1. Create a wallet with a unique test user ID
2. Retrieve the wallet by address
3. Retrieve the wallet by user ID
4. Fund the wallet with testnet USDC
5. Create a second wallet
6. Transfer tokens between wallets

## API Reference

- [Create Wallet](https://docs.crossmint.com/api-reference/wallets/create-wallet)
- [Get Wallet](https://docs.crossmint.com/api-reference/wallets/get-wallet)
- [Fund Wallet](https://docs.crossmint.com/api-reference/wallets/fund-wallet)
- [Create Transaction](https://docs.crossmint.com/api-reference/wallets/create-transaction)

## Example: Agent Wallet Flow

```typescript
// 1. Create wallet for agent
const wallet = await createWallet({ userId: 'AGENT_001' });

// 2. Fund wallet (testnet)
await fundWallet(wallet.address, 100);

// 3. Agent can now make purchases via Orders API
```

## Quick Reference: Common Workflows

### Check if a wallet is Crossmint-managed
```bash
doppler run --project mao-mao --config prd --command 'bun scripts/get-wallet.ts --address 0xYOURADDRESS --yes'
```
If it returns wallet details (Type, User, Created), it's Crossmint-managed. If error, it's external.

### Transfer ETH on Sepolia (working example)
```bash
doppler run --project mao-mao --config prd --command 'bun scripts/transfer.ts \
  --from 0xc54f836CBb4299B828b696Ef56f42edd898b3486 \
  --to 0x289155723B5d8F685D49446b2d9a4F7Bfa7606E3 \
  --amount 0.01 \
  --token eth \
  --chain ethereum-sepolia \
  --yes'
```

### Known Wallets
| Address | Owner | Chain | Notes |
|---------|-------|-------|-------|
| 0xc54f836CBb4299B828b696Ef56f42edd898b3486 | userId:francesco | ethereum-sepolia | Francesco's main testnet wallet |
| 0x289155723B5d8F685D49446b2d9a4F7Bfa7606E3 | - | ethereum-sepolia | Created 2026-01-31 |
| 0xf422bF82C48925edcb134f2a2927B8C9826A4723 | - | base-sepolia | Created 2026-01-31 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxfrancesco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
