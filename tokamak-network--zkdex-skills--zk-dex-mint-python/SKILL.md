---
name: zk-dex-mint-python
description: Generate zk-DEx mint note. Run `python generate_mint.py --owner-pk-x <hex> --owner-pk-y <hex> --value <amount>` to create a 7-input Poseidon note hash for minting. Use when this capability is needed.
metadata:
  author: tokamak-network
---

# zk-dex-mint-python

Python-based mint note generation for zk-DEx. Creates a 7-input Poseidon note hash compatible with the circom mint circuit. Uses the shared `zkdex_lib` library (pure Python, no npm/web3 dependency).

## Dependencies

- `zkdex_lib/` (shared library: Poseidon hash, Note class)
- Python 3.x
- Node.js + snarkjs (ZK proof 생성 시에만 필요)

## Usage

```bash
# 노트만 생성
python generate_mint.py \
  --owner-pk-x <hex> \
  --owner-pk-y <hex> \
  --value <amount> \
  --token-type <hex>     # optional, default: 0x0 (ETH)
  --salt <hex>           # optional, auto-generated if omitted

# 노트 + ZK proof 생성
python generate_mint.py \
  --owner-pk-x <hex> \
  --owner-pk-y <hex> \
  --value <amount> \
  --proof \
  --sk <secret_key>      # required with --proof
```

## Output Format

```json
{
  "noteHash": "0x05fa764f...",
  "noteRaw": {
    "owner0": "0x...",
    "owner1": "0x...",
    "value": "0x...",
    "token": "0x...",
    "vk0": "0x...",
    "vk1": "0x...",
    "salt": "0x..."
  },
  "proof": {
    "a": ["<uint256>", "<uint256>"],
    "b": [["<uint256>", "<uint256>"], ["<uint256>", "<uint256>"]],
    "c": ["<uint256>", "<uint256>"],
    "input": ["<noteHash>", "<value>", "<tokenType>"]
  }
}
```

- **noteHash**: `Poseidon(owner0, owner1, value, token, vk0, vk1, salt)` — 64 hex chars
- **noteRaw**: All 7 note fields as 0x-prefixed 64-char hex strings
- Regular note: `owner0=pk.x`, `owner1=pk.y`, `vk0=pk.x`, `vk1=pk.y`
- **proof** (optional): Groth16 proof formatted for Solidity verifier. Only present with `--proof` flag.
  - `a`, `b`, `c`: proof elements
  - `input`: public signals `[noteHash, value, tokenType]`

## Structure

- `generate_mint.py`: CLI script for mint note generation
- `zkdex_lib/proof.py`: ZK proof generation (Python subprocess wrapper)
- `zkdex_lib/generate_proof.js`: Node.js CLI for snarkjs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tokamak-network) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
