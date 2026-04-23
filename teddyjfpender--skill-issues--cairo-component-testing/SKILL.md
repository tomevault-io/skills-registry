---
name: cairo-component-testing
description: Explain how to test Starknet components by embedding them into mock contracts and dispatching calls; use when a request involves unit tests for component logic in Cairo. Use when this capability is needed.
metadata:
  author: teddyjfpender
---

# Cairo Component Testing

## Overview
Show how to test components by embedding them into a mock contract and using dispatchers.

## Quick Use
- Read `references/component-testing.md` before answering.
- Build a minimal contract that embeds the component.
- Use the component interface dispatcher in tests.

## Response Checklist
- Embed component storage and events into the mock contract.
- Expose component functions via impl alias with `#[abi(embed_v0)]`.
- Use a dispatcher to call component entry points from tests.

## Example Requests
- "How do I write tests for a component?"
- "Do I need a full contract to test a component?"
- "How do I call component functions in tests?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teddyjfpender) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
