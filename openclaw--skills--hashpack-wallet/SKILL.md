---
name: hashpack-wallet
description: Integrate HashPack wallet for Hedera blockchain authentication. Use for: (1) Adding HashPack login to webapps, (2) Signing Hbar transactions, (3) Connecting to Hedera DApps, (4) Getting account balance. Use when this capability is needed.
metadata:
  author: openclaw
---

# HashPack Wallet Integration

## Quick Start

```typescript
// Detect HashPack
const hashpack = (window as any).hashpack;

// Connect
const result = await hashpack.connect();

// Get account ID
const accountId = result.accountId; // e.g., "0.0.12345"
```

## Account ID Format

Hedera account IDs are format: `0.0.12345` (shard.realm.num)

## Key Methods

```typescript
// Connect (opens popup)
await hashpack.connect();

// Sign and submit transaction
const tx = new TransferTransaction()
  .addHbarTransfer(from, -10)
  .addHbarTransfer(to, 10);
await hashpack.signTransaction(tx);

// Get balance
const balance = await new AccountBalanceQuery()
  .setAccountId(accountId)
  .execute(client);

// Disconnect
hashpack.disconnect();
```

## Environment

- **Mainnet**: `https://mainnet.hashio.io/api`
- **Testnet**: `https://testnet.hashio.io/api`
- **Previewnet**: `https://previewnet.hashio.io/api`

## Transaction Types

- `TransferTransaction` - Send HBAR/tokens
- `ContractExecuteTransaction` - Call contract
- `TokenAssociateTransaction` - Associate with token
- `TokenMintTransaction` - Mint tokens
- `TopicCreateTransaction` - Create HCS topic

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
