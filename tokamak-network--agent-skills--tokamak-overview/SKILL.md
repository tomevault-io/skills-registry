---
name: tokamak-overview
description: > Use when this capability is needed.
metadata:
  author: tokamak-network
---

# Tokamak Network Overview

Tokamak Network is an "L2 On-Demand" platform on Ethereum. Operators deploy custom L2 rollup chains via the Rollup Hub, secured by TON staking economics.

## Ecosystem Architecture

```
Ethereum L1
├── TON/WTON Token Economics
│   ├── SeigManager (seigniorage distribution)
│   ├── DepositManager (staking deposits)
│   └── PowerTON (governance power)
├── DAO Governance
│   └── Agenda → Vote → Execute
└── Bridge Contracts
    └── L1 ↔ L2 message passing

Tokamak Rollup Hub (RaaS)
├── Thanos L2 (OP Stack Bedrock fork) ← current
├── Titan L2 (legacy, pre-Bedrock)
└── Custom L2 Appchains (via SDKv1)

Cross-Chain Services
├── CrossTrade (fast withdrawal, bypasses 7-day delay)
├── Standard Bridge (canonical L1↔L2)
└── DRB (Distributed Randomness Beacon)
```

## Skill Routing Guide

Use this table to find the right skill for your task:

| Your Task | Use This Skill |
|-----------|---------------|
| Query staking balance, seigniorage math, WTON decimals | **ton-staking** |
| Contract addresses, proxy pattern, TON↔WTON conversion | **tokamak-contracts** |
| Thanos L2 development, monorepo navigation, OP Stack diffs | **thanos-l2** |
| zk-EVM pipeline, Synthesizer, QAP compiler, Circom, zk-SNARK | **zk-evm** |
| L2 state channels, channel lifecycle, BridgeCore, ZK channel proofs | **private-app-channels** |
| Stealth transfers, privacy swaps, DustPool, .tok names, ERC-5564 | **dust-protocol** |
| TONStarter launchpad, TOS/sTOS/LTOS, IDO, bond market | **tonstarter** |
| CrossTrade fast withdrawal integration | **cross-trade** (coming soon) |
| DAO agenda creation, voting, governance | **dao-governance** (coming soon) |

## Universal Rules (Apply to All Tokamak Development)

### WTON Decimal Gotcha

WTON uses 27 decimals (RAY), not 18. This is the #1 source of bugs.

```solidity
// WRONG: assuming 18 decimals
uint256 amount = 100 * 1e18;  // This is 100 TON, not 100 WTON

// CORRECT: WTON uses 27 decimals
uint256 wtonAmount = 100 * 1e27;  // 100 WTON
uint256 tonAmount = 100 * 1e18;   // 100 TON
```

### Proxy Pattern

All upgradeable Tokamak contracts use `XxxProxy` + `XxxLogic`:
- Interact with the **Proxy** address (holds state)
- ABI comes from the **Logic** contract
- Never call the Logic contract directly

### npm Packages

```bash
# Thanos L2 SDK
npm install @tokamak-network/thanos-sdk

# Thanos contracts (ABIs, deploy configs)
npm install @tokamak-network/thanos-contracts

# Staking math library
npm install @tokamak-network/tokamak-staking-lib
```

### Base Layer: ethskills

For general Ethereum knowledge (Solidity patterns, EVM internals, Foundry/Hardhat usage), install [ethereum-wingman](https://github.com/austintgriffith/ethereum-wingman) as a complementary base layer. Tokamak skills focus on Tokamak-specific domain knowledge.

## Setup: Install Core Rules to CLAUDE.md

For maximum reliability, install core Tokamak rules directly into CLAUDE.md so they are **always loaded** (not dependent on skill activation):

```bash
# Project-level (recommended, version-controlled with your repo)
bash /mnt/skills/user/tokamak-overview/scripts/setup.sh --project

# Global (applies to all your projects)
bash /mnt/skills/user/tokamak-overview/scripts/setup.sh --global
```

This is idempotent — safe to run multiple times. It adds key rules (WTON decimals, proxy pattern, contract addresses) that the agent should always know.

## Key Links

- Docs: https://docs.tokamak.network
- GitHub: https://github.com/tokamak-network
- Rollup Hub: https://rolluphub.tokamak.network
- Staking: https://staking.tokamak.network

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tokamak-network) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
