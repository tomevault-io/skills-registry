---
name: pinocchio
description: Build Solana programs with Pinocchio - a lightweight, zero-copy framework. Use for "pinocchio program", "solana program", "on-chain program", "rust program", "create instruction", "PDA", "CPI", "token-2022 program", or any native Solana program development. Use when this capability is needed.
metadata:
  author: amilzbot
---

# Pinocchio

Pinocchio is a lightweight, zero-copy Solana program framework. No Anchor, minimal overhead, maximum control.

## Project Structure

Based on the [Solana Foundation template](https://github.com/solana-foundation/templates/tree/main/pinocchio/pinocchio-counter):

```
my-program/
├── Cargo.toml              # Workspace root
├── rust-toolchain.toml     # Pin Rust version (e.g., 1.92)
├── program/
│   ├── Cargo.toml
│   ├── build.rs            # Codama IDL generation
│   └── src/
│       ├── lib.rs          # declare_id!, module exports
│       ├── entrypoint.rs   # Instruction dispatch
│       ├── constants.rs    # PDA seeds, sizes
│       ├── errors.rs       # Custom errors (CodamaErrors)
│       ├── traits/         # Discriminator, Versioned, PdaSeeds
│       ├── state/          # Account structs
│       ├── instructions/   # Each instruction in its own folder
│       │   └── create_foo/
│       │       ├── mod.rs
│       │       ├── accounts.rs
│       │       ├── data.rs
│       │       ├── instruction.rs
│       │       └── processor.rs
│       └── utils/          # PDA creation, account checks
├── clients/
│   ├── rust/               # Generated Rust client
│   └── typescript/         # Generated TypeScript client
├── tests/
│   └── integration-tests/  # LiteSVM tests
├── idl/                    # Codama-generated IDL
└── scripts/
    └── generate-clients.ts # Codama client generation
```

## Core Dependencies

```toml
[workspace.dependencies]
# Pinocchio core (0.10+)
pinocchio = { version = "^0.10.1", features = ["cpi", "copy"] }
pinocchio-system = "^0.5.0"
pinocchio-log = "^0.5.1"
pinocchio-associated-token-account = "^0.3.0"
solana-address = { version = "2.0", features = ["curve25519"] }

# Token-2022 with extensions (MetadataPointer, TokenMetadata, Groups, etc.)
pinocchio-token-2022 = "^0.2.0"

# IDL generation
codama = "^0.7.2"
serde_json = "^1.0"

# Utils
thiserror = "^2.0"
const-crypto = "^0.3.0"
```

## Trait Pattern

Use traits for consistent account handling:

```rust
// traits/account.rs
pub trait Discriminator {
    const DISCRIMINATOR: u8;
}

pub trait Versioned {
    const VERSION: u8;
}

pub trait AccountSize: Discriminator + Versioned {
    const DATA_LEN: usize;
    const LEN: usize = 1 + 1 + Self::DATA_LEN; // discriminator + version + data
}

pub trait AccountDeserialize: AccountSize {
    fn from_bytes(data: &[u8]) -> Result<&Self, ProgramError>;
}

pub trait AccountSerialize: Discriminator + Versioned {
    fn to_bytes(&self) -> Vec<u8>;
    fn write_to_slice(&self, dest: &mut [u8]) -> Result<(), ProgramError>;
}
```

## Instruction Pattern

Each instruction has 4 files:

```rust
// instructions/create_config/accounts.rs
pub struct CreateConfigAccounts<'a> {
    pub payer: &'a AccountView,
    pub config: &'a AccountView,
    pub system_program: &'a AccountView,
}

impl<'a> TryFrom<&'a [AccountView]> for CreateConfigAccounts<'a> {
    type Error = ProgramError;
    
    fn try_from(accounts: &'a [AccountView]) -> Result<Self, Self::Error> {
        let [payer, config, system_program] = accounts else {
            return Err(ProgramError::NotEnoughAccountKeys);
        };
        // Validate accounts...
        Ok(Self { payer, config, system_program })
    }
}

// instructions/create_config/data.rs
pub struct CreateConfigData {
    pub bump: u8,
}

impl<'a> TryFrom<&'a [u8]> for CreateConfigData {
    type Error = ProgramError;
    
    fn try_from(data: &'a [u8]) -> Result<Self, Self::Error> {
        require_len!(data, 1);
        Ok(Self { bump: data[0] })
    }
}

// instructions/create_config/processor.rs
pub fn process_create_config(
    program_id: &Address,
    accounts: &[AccountView],
    instruction_data: &[u8],
) -> ProgramResult {
    let ix = CreateConfig::try_from((instruction_data, accounts))?;
    // Process...
    Ok(())
}
```

## Codama IDL Generation

```rust
// program/build.rs
use codama::Codama;

fn main() {
    let codama = Codama::load(Path::new(&manifest_dir)).unwrap();
    let idl_json = codama.get_json_idl().unwrap();
    // Write to idl/my_program.json
}

// instructions/definition.rs (for IDL generation)
use codama::CodamaInstructions;

#[derive(CodamaInstructions)]
pub enum MyProgramInstruction {
    #[codama(account(name = "payer", signer, writable))]
    #[codama(account(name = "config", writable))]
    #[codama(account(name = "system_program"))]
    CreateConfig { bump: u8 } = 0,
}
```

## LiteSVM Testing

```rust
// tests/integration-tests/src/utils/setup.rs
use litesvm::LiteSVM;

pub struct TestContext {
    pub svm: LiteSVM,
    pub payer: Keypair,
}

impl TestContext {
    pub fn new() -> Self {
        let mut svm = LiteSVM::new().with_sysvars().with_default_programs();
        let program_data = include_bytes!("../../../../target/deploy/my_program.so");
        svm.add_program(PROGRAM_ID, program_data);
        // ...
    }
}
```

## Token-2022 Extension Order ⚠️

**Critical**: When using Token-2022 extensions like MetadataPointer + TokenMetadata:

```rust
// 1. Create account with INITIAL size (pointers only)
create_pda_account(
    payer,
    rent,
    234,        // MetadataPointer size only
    &TOKEN_2022_PROGRAM_ID,
    mint,
    seeds,
    Some(500),  // Final size for rent calculation
)?;

// 2. Initialize pointer extensions BEFORE InitMint2
InitializeMetadataPointer {
    mint,
    authority: Some(*authority),
    metadata_address: Some(*mint), // Self-referencing
}.invoke()?;

// 3. Initialize mint
InitializeMint2 {
    mint,
    decimals: 0,
    mint_authority: authority,
    freeze_authority: Some(authority),
}.invoke(TokenProgramVariant::Token2022)?;

// 4. Initialize metadata content AFTER mint exists
InitializeTokenMetadata {
    metadata: mint,
    update_authority: authority,
    mint,
    mint_authority: authority,
    name: "My NFT",
    symbol: "NFT",
    uri: "sha256:...",  // Can store proof hash here
}.invoke_signed(&signers)?;
```

**Why this order?** InitMint2 fails if there's uninitialized extension space. The runtime auto-expands for TokenMetadata when it's initialized after the mint.

## Best Practices

1. **Use the template** — Clone from solana-foundation/templates
2. **Use traits** — Consistent serialization, versioning
3. **Use Codama** — Auto-generate clients from IDL
4. **Test with LiteSVM** — Fast local testing before devnet
5. **Pad accounts** — Reserve space for upgrades
6. **Explicit errors** — Use `CodamaErrors` derive

## Reference Files

For detailed patterns:
- **`references/state.md`** — Account state, serialization, PDAs
- **`references/instructions.md`** — Instruction processors, account parsing
- **`references/token-2022.md`** — Token-2022 extensions, groups, metadata

## Resources

- [Pinocchio GitHub](https://github.com/anza-xyz/pinocchio)
- [Solana Foundation Templates](https://github.com/solana-foundation/templates)
- [Attestation Service (Token-2022 reference)](https://github.com/solana-foundation/solana-attestation-service)
- [Codama Crate](https://crates.io/crates/codama)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amilzbot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
