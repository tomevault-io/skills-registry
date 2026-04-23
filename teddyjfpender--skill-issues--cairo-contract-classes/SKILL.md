---
name: cairo-contract-classes
description: Explain Starknet contract classes vs instances, class hash, contract deployment, and upgrade patterns; use when a request involves declaring/deploying contracts or understanding class hashes in Cairo. Use when this capability is needed.
metadata:
  author: teddyjfpender
---

# Cairo Contract Classes and Instances

## Overview
Clarify what a contract class is, how instances are deployed, and how class hashes relate to code and ABI.

## Quick Use
- Read `references/contract-classes.md` before answering.
- Distinguish class (code + ABI) from instance (storage + address).
- Mention class hash and contract address derivation when deployment is discussed.

## Response Checklist
- Use "class" for the compiled contract definition and "instance" for a deployed contract.
- Note that a class is immutable; upgrades require a proxy pattern.
- Mention declare vs deploy and when each step is required.

## Example Requests
- "What is the difference between a class and a contract instance?"
- "How is a contract address computed?"
- "Why do I need to declare before deploying?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teddyjfpender) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
