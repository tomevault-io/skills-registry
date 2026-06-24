---
name: tokamak-contracts
description: > Use when this capability is needed.
metadata:
  author: tokamak-network
---

# Tokamak Contracts

Patterns, addresses, and gotchas for developing Solidity contracts on Tokamak Network infrastructure.

## #1 Gotcha: Proxy Pattern

All Tokamak upgradeable contracts use `XxxProxy` + `XxxLogic`:

```
XxxProxy  ← interact with THIS (holds storage/state)
XxxLogic  ← ABI source only (stateless implementation)
```

```solidity
// WRONG: calling the Logic contract directly
SeigManagerLogic logic = SeigManagerLogic(LOGIC_ADDRESS);
logic.stakeOf(layer2, account);  // reads wrong storage!

// CORRECT: call the Proxy with the Logic's ABI
SeigManagerLogic seigManager = SeigManagerLogic(PROXY_ADDRESS);
seigManager.stakeOf(layer2, account);  // reads proxy's storage
```

### How It Works

1. `XxxProxy` stores all state variables and delegates calls to `XxxLogic`
2. Upgrades change the Logic address inside the Proxy (via DAO governance)
3. The Proxy address never changes — it's the canonical entry point
4. Storage layout must be compatible across Logic versions

### Checking Implementation

```bash
# Get the implementation address from a proxy
cast storage <PROXY_ADDRESS> 0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc --rpc-url https://eth.llamarpc.com
```

> Detailed proxy pattern internals: See [references/proxy-pattern.md](references/proxy-pattern.md)

## #2 Gotcha: TON vs WTON

| Property | TON | WTON |
|----------|-----|------|
| Decimals | 18 (WAD) | 27 (RAY) |
| Use case | Wallet balance, transfers | Staking, governance, internal math |
| Conversion | `WTON.swapFromTON(amount)` | `WTON.swapToTON(amount)` |

```solidity
// WRONG: sending TON directly to staking
depositManager.deposit(layer2, tonAmount);  // fails or wrong amount

// CORRECT: convert TON→WTON first, then deposit
wton.swapFromTON(tonAmount);
wton.approve(address(depositManager), wtonAmount);
depositManager.deposit(layer2, wtonAmount);
```

## Core Contract Addresses (Quick Reference)

### Mainnet

| Contract | Address |
|----------|---------|
| TON | `0x2be5e8c109e2197D077D13A82dAead6a9b3433C5` |
| WTON | `0xc4A11aaf6ea915Ed7Ac194161d2fC9384F15bff2` |
| SeigManagerProxy | `0x0b55a0f463b6defb81c6063973763951712d0e5f` |
| DepositManagerProxy | `0x0b58ca72b12f01fc05f8f252e226f3e2089bd00e` |
| Layer2RegistryProxy | `0x0b3E174A2170083e770D5d4Cf56774D221b7063e` |
| PowerTONProxy | `0x970298189050aBd4dc4F119ccae14ee145ad9371` |

### Sepolia

| Contract | Address |
|----------|---------|
| TON | `0xa30fe40285b8f5c0457dbc3b7c8a280373c40044` |
| WTON | `0x79e0d92670106c85e9067b56b8f674340dca0bbd` |
| SeigManagerProxy | `0x2320542ae933FbAdf8f5B97cA348c7CeDA90fAd7` |
| DepositManagerProxy | `0x90ffcc7F168DceDBEF1Cb6c6eB00cA73F922956F` |

> Full JSON address lists: See [references/addresses-mainnet.json](references/addresses-mainnet.json) and [references/addresses-sepolia.json](references/addresses-sepolia.json)

## Contract Verification Script

Verify that contract addresses are live and return expected values:

```bash
bash /mnt/skills/user/tokamak-contracts/scripts/verify-addresses.sh [network]
```

## Solidity Version Notes

| Repository | Solidity | Tooling |
|-----------|----------|---------|
| ton-staking-v2 | 0.5.12 | Truffle |
| tokamak-dao-contracts | 0.5.12 | Truffle |
| tokamak-thanos | 0.8.x | Hardhat/Foundry |
| CrossTrade | 0.8.x | Hardhat |

Be aware that older contracts (staking, DAO) use Solidity 0.5.x patterns:
- No custom errors (use `require` with string messages)
- No immutable variables
- Different constructor syntax

## npm Packages

```bash
# Thanos L2 contracts (ABIs, deploy configs, genesis)
npm install @tokamak-network/thanos-contracts

# Thanos SDK (CrossChainMessenger, gas estimation)
npm install @tokamak-network/thanos-sdk

# Staking math library
npm install @tokamak-network/tokamak-staking-lib
```

## Common Patterns

### Reading from Proxy Contracts

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://eth.llamarpc.com");

// Always use PROXY address with LOGIC ABI
const seigManager = new ethers.Contract(
  "0x0b55a0f463b6defb81c6063973763951712d0e5f",  // Proxy
  ["function stakeOf(address,address) view returns (uint256)"],
  provider
);

const staked = await seigManager.stakeOf(layer2, account);
console.log(ethers.formatUnits(staked, 27));  // 27 decimals!
```

### Checking if Address is a Proxy

```bash
# EIP-1967 implementation slot
cast storage <ADDRESS> 0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc
# Non-zero result = proxy contract
```

## Related Skills

- **ton-staking**: staking-specific functions and seigniorage math
- **thanos-l2**: Thanos L2 contract deployment and interaction
- **cross-trade**: CrossTrade contract interfaces

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tokamak-network) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
