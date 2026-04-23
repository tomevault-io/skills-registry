---
name: contract-deployer
description: Deploy smart contracts (ERC20, ERC721) from templates. Use when this capability is needed.
metadata:
  author: termix-official
---

# Contract Deployer Skill

Deploy smart contracts (ERC20, ERC721) from templates.

## Overview

Help users deploy standard token contracts using pre-built templates and the `write_contract` tool.

## Capabilities

- **Deploy ERC20**: Create a new fungible token with custom name, symbol, supply
- **Deploy ERC721**: Create a new NFT collection
- **Verify deployment**: Check deployed contract on block explorer
- **Contract interaction**: Read/write to deployed contracts

## Tools Used

- `write_contract` - Deploy contract bytecode
- `is_contract` - Verify deployment
- `read_contract` - Read contract state
- `estimate_gas` - Estimate deployment cost

## Security Rules

- ALWAYS show estimated deployment gas cost before executing
- ALWAYS confirm contract parameters (name, symbol, supply) with user
- Warn about mainnet deployments (suggest testnet first)
- Never deploy without explicit confirmation

## Example Interactions

User: "Deploy an ERC20 token called MyCoin with 1M supply on BSC"
Action: Show deployment parameters, gas estimate, confirm, deploy using ERC20 template

User: "Create an NFT collection on Polygon"
Action: Collect name/symbol, show gas estimate, deploy ERC721 template

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/termix-official) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
