---
name: bitcoin-libraries-ldk
description: | Use when this capability is needed.
metadata:
  author: claude-dev-suite
---

# LDK (Lightning Dev Kit)

Modular Rust library for Lightning. Used by Mutiny Wallet, Cash App,
Casa, ldk-node, more.

See full overview at [../../lightning/ldk/SKILL.md](../../lightning/ldk/SKILL.md).

## Crates

- `lightning` — core protocol logic.
- `lightning-net-tokio` — async TCP transport.
- `lightning-persister` — file persistence.
- `lightning-background-processor` — task driver.
- `lightning-block-sync` — chain sync via bitcoind RPC.
- `lightning-transaction-sync` — chain sync via Esplora / Electrum.
- `lightning-rapid-gossip-sync` — fast initial gossip.
- `lightning-invoice` — BOLT11 invoice handling.
- `ldk-node` — opinionated bundled crate.
- `lightning-liquidity` — LSP client/server.

## ldk-node quickstart

```toml
[dependencies]
ldk-node = "0.4"
```

```rust
use ldk_node::{Builder, Network};

let builder = Builder::new()
    .set_network(Network::Bitcoin)
    .set_chain_source_esplora("https://mempool.space/api".into())
    .set_storage_dir_path("/path/to/data".into());
let node = builder.build()?;
node.start()?;

let invoice = node.bolt11_payment().receive(
    100_000_000,    // 100k msat
    "Test", 3600,
)?;
```

## Bindings

- **LDKSwift** — iOS/macOS.
- **LDKKotlin / LDKAndroid** — Android.
- **ldk-node-jvm** — JVM.
- **ldk-node-js** — JS/WASM.

## Use cases

- Mobile Lightning wallets.
- Browser-based LN (Mutiny via WASM).
- Custom backend services with Lightning embedded.

## Compared

See [../../lightning/ldk/SKILL.md](../../lightning/ldk/SKILL.md) for
LND/CLN comparison and full feature matrix.

## Common pitfalls

- `Persist` trait misimplementation → state corruption.
- Async signer race conditions.
- Version migrations require careful state migration.

## See also

- [../../lightning/ldk/SKILL.md](../../lightning/ldk/SKILL.md)
- [rust-bitcoin/SKILL.md](../rust-bitcoin/SKILL.md)
- [bdk/SKILL.md](../bdk/SKILL.md)

---
> Source: [claude-dev-suite/claude-dev-suite](https://github.com/claude-dev-suite/claude-dev-suite) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
