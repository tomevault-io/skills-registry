---
name: cairo-components-under-hood
description: Explain how components are expanded under the hood, including HasComponent and ComponentState; use when a request involves debugging or understanding generated component code in Cairo. Use when this capability is needed.
metadata:
  author: teddyjfpender
---

# Cairo Components Under the Hood

## Overview
Explain the generated traits and wrappers created by component macros and how they connect to the host contract.

## Quick Use
- Read `references/components-under-hood.md` before answering.
- Mention HasComponent methods for access and mutation.
- Explain how embeddable impls become contract entry points.

## Response Checklist
- Identify the generated `HasComponent` trait and its methods.
- Explain `ComponentState<TContractState>` as the bridge to host storage.
- Clarify how impl aliases and `#[abi(embed_v0)]` expose entry points.

## Example Requests
- "What does component! generate behind the scenes?"
- "How does ComponentState work?"
- "Why do I need HasComponent for embedding?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teddyjfpender) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
