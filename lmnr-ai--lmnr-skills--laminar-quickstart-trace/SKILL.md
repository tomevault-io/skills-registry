---
name: laminar-quickstart-trace
description: Create a minimal Laminar trace demo in minutes with no external LLM calls. Use when a user asks for a Laminar example, a quickstart demo, or wants to see traces appear in the Laminar UI quickly (cloud or self-hosted). Use when this capability is needed.
metadata:
  author: lmnr-ai
---

# Laminar Quickstart Trace

## Workflow

1. Confirm runtime (Node/TS vs Python), Laminar backend target (cloud vs self-hosted), and availability of `LMNR_PROJECT_API_KEY`.
2. Build the smallest runnable demo using manual spans only; tag the trace with a unique run id for easy filtering.
3. Run the script and direct the user to the Traces view; filter by tag or metadata to find the trace.
4. If traces do not show quickly, retry with batching disabled and call `Laminar.flush()`.

## References

- `references/quickstart-node.md` for a Node/JS demo with code and commands.
- `references/quickstart-python.md` for a Python demo with code and commands.
- `references/troubleshooting.md` for common failures and fast fixes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lmnr-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
