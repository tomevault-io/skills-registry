---
name: zk-dex-keygen-python
description: Generate BabyJubJub keypair for zk-DEx. Run `python generate_keypair.py -p <password>` to create sk/pk with Poseidon-based address and encrypted keystore JSON. Use when this capability is needed.
metadata:
  author: tokamak-network
---

# zk-dex-keygen-python

Python-based BabyJubJub keypair generation for zk-DEx. Generates keypairs using the BabyJubJub curve and exports them in encrypted keystore JSON format. Address derivation uses **Poseidon hash** (circomlibjs-compatible), matching the on-chain circuit implementation.

## Dependencies

- `sapling_jubjub.py` (BabyJubJub curve arithmetic, from `baby_jubjub_ecc`)
- `zkdex_lib/` (shared library: Poseidon hash, Account, Note)
- `cryptography` Python package (for AES-256-GCM encryption)
- Python 3.x

## Usage

```bash
python generate_keypair.py --password <password>
# or
python generate_keypair.py -p <password>
```

## Output Format

```json
{
  "address": "914e04dccf3cd308ad6d0848df14ea5752e2b298",
  "publicKey": {
    "x": "0x...",
    "y": "0x..."
  },
  "keystore": {
    "crypto": {
      "cipher": "aes-256-gcm",
      "ciphertext": "...",
      "cipherparams": { "iv": "..." },
      "kdf": "scrypt",
      "kdfparams": { "n": 16384, "r": 8, "p": 1, "dklen": 32, "salt": "..." },
      "mac": "..."
    },
    "version": 1
  },
  "exportedAt": 1770846855188
}
```

- **address**: `truncate_to_160_bits(Poseidon(pk.x, pk.y))` — 40 hex chars, circomlibjs `pubKeyToAddress` 호환
- **publicKey**: BabyJubJub public key coordinates with `0x` prefix (64 hex chars each)
- **keystore**: Private key encrypted with scrypt KDF + AES-256-GCM
- **exportedAt**: Millisecond timestamp

## Structure

- `generate_keypair.py`: Main script — keypair generation, Poseidon address derivation, keystore export
- `sapling_jubjub.py`: BabyJubJub curve field and point arithmetic
- `sapling_utils.py`: Bit/byte conversion utilities

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tokamak-network) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
