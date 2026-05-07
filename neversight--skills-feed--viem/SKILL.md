---
name: viem
description: TypeScript interface for Ethereum — clients, contracts, accounts, chains, ENS, and utilities. Use when this capability is needed.
metadata:
  author: neversight
---

> Skill based on viem, generated 2026-02-09. Docs: https://viem.sh

viem is a TypeScript client for Ethereum: Public/Wallet/Test clients, transports (HTTP, WebSocket, custom), type-safe contract reads/writes, local and JSON-RPC accounts, chains, ENS, and encoding/unit helpers.

## Core References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Clients & Transports | Public/Wallet/Test clients, HTTP/WS/custom transports, multicall batching | [core-clients-transports](references/core-clients-transports.md) |
| Contract | getContract, readContract, writeContract, simulateContract | [core-contract](references/core-contract.md) |
| Accounts | Local (privateKey, mnemonic) and JSON-RPC accounts, extend with publicActions | [core-accounts](references/core-accounts.md) |

## Features

### Chains & ENS

| Topic | Description | Reference |
|-------|-------------|-----------|
| Chains | Built-in chains (viem/chains), defineChain for custom | [features-chains](references/features-chains.md) |
| ENS | getEnsAddress, getEnsName, getEnsAvatar, normalize | [features-ens](references/features-ens.md) |

### Utilities

| Topic | Description | Reference |
|-------|-------------|-----------|
| Utilities | getAddress, parseEther/formatEther, encoding, keccak256, ABI helpers | [features-utilities](references/features-utilities.md) |

## Best Practices

| Topic | Description | Reference |
|-------|-------------|-----------|
| Contract & Errors | Simulate before write, typed error handling, RPC/security | [best-practices-contract-and-errors](references/best-practices-contract-and-errors.md) |

## External Links

- [viem.sh](https://viem.sh)
- [wevm/viem GitHub](https://github.com/wevm/viem)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
