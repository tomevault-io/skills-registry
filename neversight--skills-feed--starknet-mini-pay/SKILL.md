---
name: starknet-mini-pay
description: Simple P2P payments on Starknet. Generate QR codes, payment links, invoices, and transfer ETH/STRK/USDC. Like Lightning, but native. Use when this capability is needed.
metadata:
  author: neversight
---

# Starknet Mini-Pay Skill

Simple P2P payments on Starknet. Like Lightning, but native.

## Overview

```
┌─────────────────────────────────────────────────────────┐
│                    STARKNET MINI-PAY                    │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐ │
│  │  QR Codes   │  │  Payment    │  │   Telegram Bot  │ │
│  │  Generator  │  │   Links     │  │   Interface     │ │
│  └─────────────┘  └─────────────┘  └─────────────────┘ │
│           │                │                   │          │
│           └────────────────┴───────────────────┘          │
│                         │                                │
│              ┌─────────▼─────────┐                       │
│              │   starknet-py    │                       │
│              │   SDK + RPC      │                       │
│              └──────────────────┘                       │
│                         │                                │
│         ┌───────────────┼───────────────┐               │
│         ▼               ▼               ▼               │
│    ┌─────────┐    ┌──────────┐    ┌──────────┐         │
│    │ ArgentX │    │  Braavos │    │  Generic  │         │
│    │ Account │    │ Account  │    │  Account  │         │
│    └─────────┘    └──────────┘    └──────────┘         │
└─────────────────────────────────────────────────────────┘
```

## Features

| Feature | Description |
|---------|-------------|
| **P2P Transfers** | Send ETH/STRK/USDC to any Starknet address |
| **QR Codes** | Generate QR codes for addresses (scan to pay) |
| **Payment Links** | `starknet:<addr>?amount=1&memo=coffee` |
| **Invoice System** | Generate payment requests with expiry |
| **Telegram Bot** | Send/receive via Telegram commands |
| **Transaction History** | Track all transfers with status |

## Quick Start

### CLI Usage

```bash
# Send payment
python3.12 scripts/cli.py send 0x123... 0.5 --memo "coffee"

# Generate QR code for your address
python3.12 scripts/cli.py qr 0x123... --output qr.png

# Create payment link
python3.12 scripts/cli.py link 0x123... --amount 0.1 --memo "lunch"

# Create invoice
python3.12 scripts/cli.py invoice 0x123... 25.00 --expires 1h

# Check transaction status
python3.12 scripts/cli.py status 0xabcdef...

# Balance check
python3.12 scripts/cli.py balance 0x123...
```

### Payment Link Format

```
starknet:<address>?amount=<value>&memo=<text>&token=<ETH|STRK|USDC>
```

**Example:**
```
starknet:0x053c91253bc9682c04929ca02ed00b3e423f6714d2ea42d73d1b8f3f8d400005?amount=0.01&memo=coffee&token=ETH
```

### Telegram Bot Commands

```
/pay <address> <amount> [memo]  - Send payment
/qr                          - Show your QR code
/balance                     - Check balance
/link <amount> [memo]       - Generate payment link
/invoice <amount>           - Create invoice
/history                     - Transaction history
/help                        - Show help
```

## Architecture

```
starknet-mini-pay/
├── SKILL.md
├── README.md
├── scripts/
│   ├── cli.py              # Main CLI interface
│   ├── mini_pay.py         # Core payment logic
│   ├── qr_generator.py     # QR code generation
│   ├── link_builder.py     # Payment link builder
│   ├── invoice.py          # Invoice system
│   ├── telegram_bot.py     # Telegram bot
│   └── starknet_client.py  # Starknet RPC client
├── contracts/
│   └── payment_request.cairo  # Optional invoice contract
└── tests/
    └── test_payments.py
```

## Dependencies

```bash
pip install starknet-py --break-system-packages
pip install qrcode[pil] --break-system-packages
pip install python-telegram-bot --break-system-packages
pip install httpx aiosqlite --break-system-packages
```

## Configuration

```bash
# Environment variables
export STARKNET_RPC="https://rpc.starknet.lava.build:443"
export MINI_PAY_PRIVATE_KEY="0x..."
export MINI_PAY_ADDRESS="0x..."
export TELEGRAM_BOT_TOKEN="..."
export TELEGRAM_CHAT_ID="..."
```

## Core Functions

### Send Payment

```python
from mini_pay import MiniPay

pay = MiniPay(rpc_url="https://rpc.starknet.lava.build:443")

# Send ETH
tx_hash = pay.send(
    from_address="0x...",
    private_key="0x...",
    to_address="0x123...",
    amount_wei=0.5 * 10**18,
    token="ETH"
)

# Check status
status = pay.get_status(tx_hash)
print(f"Status: {status}")  # PENDING, CONFIRMED, FAILED
```

### Generate QR Code

```python
from qr_generator import QRGenerator

qr = QRGenerator()
qr.generate(
    address="0x053c91253bc9682c04929ca02ed00b3e423f6714d2ea42d73d1b8f3f8d400005",
    amount=None,  # Optional amount
    memo=None,     # Optional memo
    output_file="address_qr.png"
)
```

### Payment Links

```python
from link_builder import PaymentLink

link = PaymentLink()

# Create link
url = link.create(
    address="0x123...",
    amount=0.01,
    memo="coffee",
    token="ETH"
)

# Parse incoming link
data = link.parse("starknet:0x123...?amount=0.01&memo=coffee")
```

### Invoice System

```python
from invoice import InvoiceManager

invoice = InvoiceManager()

# Create invoice
invoice_data = invoice.create(
    payer_address="0x...",
    amount=25.00,
    token="USDC",
    expiry_seconds=3600,  # 1 hour
    description="Payment for services"
)

# Check invoice status
status = invoice.get_status(invoice_data.id)
```

## Telegram Bot

### Run Bot

```bash
python3.12 scripts/telegram_bot.py
```

### Bot Flow

```
User: /pay 0x123... 0.5 coffee
Bot:  📤 Sending 0.5 ETH to 0x123... (memo: coffee)
Bot:  ⏳ Transaction pending: 0xabc...
Bot:  ✅ Confirmed in block #12345
```

## Optional: Invoice Contract

For trustless invoices, deploy the Cairo contract:

```cairo
// contracts/payment_request.cairo

#[starknet::contract]
mod PaymentRequest {
    #[storage]
    struct Storage {
        request_id: u256,
        requests: Map<u256, Request>,
        owner: ContractAddress,
    }

    #[derive(Drop, Serde)]
    struct Request {
        amount: u256,
        token: ContractAddress,
        recipient: ContractAddress,
        expiry: u64,
        fulfilled: bool,
        memo: felt252,
    }

    #[external(v0)]
    impl IPaymentRequestImpl of IPaymentRequest<ContractState> {
        fn create_request(
            ref self: ContractState,
            amount: u256,
            token: ContractAddress,
            expiry: u64,
            memo: felt252
        ) -> u256 {
            // Create payment request
        }

        fn fulfill_request(
            ref self: ContractState,
            request_id: u256
        ) {
            // Execute payment
        }
    }
}
```

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| `INSUFFICIENT_BALANCE` | Not enough ETH for transfer | Add more ETH to account |
| `ACCOUNT_NOT_FOUND` | Invalid sender address | Check address format |
| `INVALID_AMOUNT` | Amount <= 0 | Use positive amount |
| `TX_FAILED` | Transaction reverted | Check recipient address |
| `INVOICE_EXPIRED` | Invoice past expiry | Create new invoice |

## Payment Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Sender    │     │  Mini-Pay   │     │  Recipient  │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
       │  1. Initiate      │                   │
       │──────────────────▶│                   │
       │                   │                   │
       │                   │  2. Execute      │
       │                   │  ETH Transfer    │
       │                   │─────────────────▶
       │                   │                   │
       │                   │  3. Confirm      │
       │                   │◀──────────────────
       │  4. Success       │                   │
       │◀──────────────────│                   │
       │                   │                   │
```

## Security Notes

- **Private keys**: Never expose in logs or error messages
- **Amount validation**: Prevent negative/zero amounts
- **Expiry checks**: Invoices expire automatically
- **Address format**: Validate Starknet addresses (0x... format)

## Comparison with Lightning

| Feature | Lightning | Starknet Mini-Pay |
|---------|-----------|-------------------|
| Setup time | Hours (channels) | Instant |
| Privacy | Onion routing | Native (per txn) |
| Speed | ~1 sec | ~2-3 min |
| Fees | Variable | Predictable |
| Custody | Lightning node | Self-custody |
| Native | No (needs BTC) | Yes (Starknet native) |

## Roadmap

- [ ] Multi-token support (STRK, USDC, DAI)
- [ ] Batch payments
- [ ] Payment requests via IPFS
- [ ] Web interface
- [ ] Mobile app (Flutter)
- [ ] MPC wallet integration

## Resources

- [Starknet Docs](https://docs.starknet.io/)
- [starknet-py](https://github.com/starknet-io/starknet-py)
- [Argent X](https://www.argent.xyz/argent-x/)
- [Braavos](https://braavos.app/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
