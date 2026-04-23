---
name: cairo-components
description: Explain Starknet components, embedding into contracts, and component interfaces; use when a request involves reusable contract logic or component-based design in Cairo. Use when this capability is needed.
metadata:
  author: teddyjfpender
---

# Cairo Components

## Overview
Explain how to define, embed, and use components to share contract logic safely.

## Quick Use
- Read `references/components.md` before answering.
- Use `#[starknet::component]` and `component!` for embedding.
- Show both embeddable external functions and internal functions.

## Response Checklist
- Define component storage with `#[storage]` and an `Event` enum.
- Use `#[embeddable_as]` on impls to expose entry points.
- Embed component storage and events in the host contract.
- Add `#[abi(embed_v0)]` impl aliases when embedding.

## Example Requests
- "How do I create a reusable component?"
- "How do I embed a component into a contract?"
- "What is the difference between embeddable and internal component functions?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teddyjfpender) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
