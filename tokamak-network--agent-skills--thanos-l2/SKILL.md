---
name: thanos-l2
description: > Use when this capability is needed.
metadata:
  author: tokamak-network
---

# Thanos L2 Development

Thanos is Tokamak Network's current L2 rollup stack, forked from OP Stack Bedrock. It powers production rollup chains deployed via the Tokamak Rollup Hub.

**Source**: https://github.com/tokamak-network/tokamak-thanos

## Thanos vs Vanilla OP Stack

Thanos adds Tokamak-specific features on top of the standard OP Stack:

| Feature | OP Stack | Thanos |
|---------|----------|--------|
| Seigniorage | No | Yes (via SeigManager integration) |
| Native TON support | No | Yes (TON as native gas option) |
| CrossTrade | No | Yes (fast withdrawal service) |
| DRB integration | No | Yes (on-chain randomness) |
| USDC bridge | Standard bridge | Custom USDC bridge contract |
| Deployment | Manual | Rollup Hub SDKv1 (one-command) |

All standard OP Stack development knowledge applies. Thanos adds the above Tokamak-specific layers.

## Monorepo Structure

```
tokamak-thanos/
├── op-batcher/         # Batches L2 transactions → L1
├── op-node/            # Derives L2 chain from L1 data
├── op-proposer/        # Proposes L2 output roots to L1
├── op-challenger/      # Disputes invalid proposals
├── op-program/         # Fault proof program
├── op-chain-ops/       # Chain operations and genesis tools
├── op-service/         # Shared service utilities
├── op-e2e/             # End-to-end tests
├── packages/
│   ├── contracts-bedrock/   # L1 and L2 Solidity contracts
│   │   ├── src/L1/          # L1 bridge, portal, dispute
│   │   ├── src/L2/          # L2 precompiles, predeploys
│   │   └── deploy-config/   # Network deploy configurations
│   ├── sdk/                 # @tokamak-network/thanos-sdk
│   └── chain-mon/           # Chain monitoring tools
└── specs/                   # Protocol specifications
```

> Detailed module descriptions: See [references/monorepo-guide.md](references/monorepo-guide.md)

## SDK: @tokamak-network/thanos-sdk

### Installation

```bash
npm install @tokamak-network/thanos-sdk
```

### CrossChainMessenger (L1 ↔ L2 bridging)

```typescript
import { CrossChainMessenger } from "@tokamak-network/thanos-sdk";
import { ethers } from "ethers";

const messenger = new CrossChainMessenger({
  l1ChainId: 1,
  l2ChainId: 55004,  // Thanos chain ID (varies per deployment)
  l1SignerOrProvider: l1Signer,
  l2SignerOrProvider: l2Signer,
});

// Deposit ETH from L1 → L2
const tx = await messenger.depositETH(ethers.parseEther("0.1"));
await messenger.waitForMessageReceipt(tx);

// Deposit ERC20 from L1 → L2
await messenger.approveERC20(l1Token, l2Token, amount);
const depositTx = await messenger.depositERC20(l1Token, l2Token, amount);

// Withdraw from L2 → L1 (7-day challenge period)
const withdrawTx = await messenger.withdrawETH(ethers.parseEther("0.1"));
// Must wait for challenge period, then finalize
```

### Gas Estimation

```typescript
import { asL2Provider } from "@tokamak-network/thanos-sdk";

const l2Provider = asL2Provider(new ethers.JsonRpcProvider(l2RpcUrl));
const gasInfo = await l2Provider.estimateTotalGasCost(tx);
console.log("L1 data fee:", gasInfo.l1GasCost);
console.log("L2 execution:", gasInfo.l2GasCost);
```

## Local Development

### Devnet Setup

The fastest way to run a local Thanos L2:

```bash
git clone https://github.com/tokamak-network/tokamak-thanos.git
cd tokamak-thanos

# Build all Go binaries
make build

# Start local devnet (L1 geth + L2 components)
make devnet-up

# Devnet L1 RPC: http://localhost:8545
# Devnet L2 RPC: http://localhost:9545
```

### Deploying Contracts on Thanos L2

```bash
# Using Foundry
forge create --rpc-url <L2_RPC_URL> \
  --private-key <PRIVATE_KEY> \
  src/MyContract.sol:MyContract

# Using Hardhat
npx hardhat deploy --network thanos-devnet
```

Hardhat config:

```javascript
// hardhat.config.js
module.exports = {
  networks: {
    "thanos-devnet": {
      url: "http://localhost:9545",
      accounts: [PRIVATE_KEY],
    },
  },
};
```

## Key Differences from Standard OP Stack

### 1. Native TON Gas

Thanos chains can optionally use TON as the native gas token instead of ETH:
- Gas fees paid in TON
- L1→L2 bridge handles TON natively
- `msg.value` denominated in TON (not ETH) on TON-gas chains

### 2. Seigniorage Integration

Thanos L2 operators earn seigniorage rewards from the L1 SeigManager:
- Reward proportional to TVL in the L2's bridge
- Distributed automatically per L1 block
- Configured during Rollup Hub deployment

### 3. USDC Bridge

Custom USDC bridge contract for native USDC transfers (not wrapped):
- L1 locks USDC in escrow
- L2 mints native USDC
- Withdrawal burns L2 USDC and releases L1 USDC

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Using standard OP SDK instead of Thanos SDK | `npm install @tokamak-network/thanos-sdk` |
| Assuming ETH as gas token | Check chain config — may be TON |
| Not accounting for L1 data fee in gas estimation | Use `asL2Provider` + `estimateTotalGasCost` |
| Hardcoding L2 chain IDs | Chain ID varies per Rollup Hub deployment |
| Using `optimism` Hardhat plugin | Use Thanos-specific deploy configs |

## Related Skills

- **tokamak-contracts**: Contract addresses and proxy patterns
- **ton-staking**: How seigniorage rewards work
- **cross-trade**: Fast withdrawal bypassing 7-day delay

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tokamak-network) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
