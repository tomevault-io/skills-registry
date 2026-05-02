---
name: sharpclaw
description: > Use when this capability is needed.
metadata:
  author: garfeildma
---

# SharpClaw - Solana Meme-Coin Trading Bot

You control a running SharpClaw trading bot instance. The bot runs as a background systemd service (`claw-trading.service`) and executes trades autonomously. You interact with it by querying its SQLite database and using its CLI.

## How to Send Commands

SharpClaw exposes a CLI for direct interaction. Run commands via:

```bash
cd ~/claw_trading && bun run src/cli.ts <command> [args...]
```

To query the database directly (for read-only operations):

```bash
cd ~/claw_trading && sqlite3 data/trading.db "<SQL query>"
```

## Available Commands

### Trading

**Buy a token:**
```bash
bun run src/cli.ts buy <mint_address> <sol_amount>
```
Example: `bun run src/cli.ts buy So11111111111111111111111111111111111111112 0.1`

**Sell a token:**
```bash
bun run src/cli.ts sell <mint_address> [token_amount]
```
Omit amount to sell entire position.

### Portfolio & Status

**Check wallet balance:**
```bash
bun run src/cli.ts balance
```
Returns SOL balance and all token holdings.

**View open positions:**
```bash
bun run src/cli.ts positions
```
Shows each position with entry price, current price, P&L %, and stop-loss level.

**Today's P&L:**
```bash
bun run src/cli.ts pnl
```
Returns daily realized P&L, per-strategy expectancy, win rates, R:R ratios, and adaptive factor status (confidence scale, stop adjustments, auto-disable).

**Recent trades:**
```bash
bun run src/cli.ts trades
```

**Bot status:**
```bash
bun run src/cli.ts status
```
Shows: auto-trading on/off, circuit breaker state, total exposure, daily loss tracking.

### Analysis

**AI-powered token analysis:**
```bash
bun run src/cli.ts analyze <mint_address>
```
Runs full pipeline: technical indicators (RSI, MACD, volume), social sentiment (via xAI Grok or Twitter), rug pull check, and AI recommendation with confidence score.

### Watchlist

**Add to watchlist:**
```bash
bun run src/cli.ts watch <mint_address> [symbol]
```

**Remove from watchlist:**
```bash
bun run src/cli.ts unwatch <mint_address>
```

**View watchlist:**
```bash
bun run src/cli.ts watchlist
```

### Auto-Trading Control

**Pause auto-trading:**
```bash
bun run src/cli.ts pause
```
Stops new entries. Existing positions continue to be managed (stop-loss/take-profit still active).

**Resume auto-trading:**
```bash
bun run src/cli.ts resume
```

## Service Management

Check if the bot is running:
```bash
systemctl is-active claw-trading
```

View recent logs:
```bash
journalctl -u claw-trading --no-pager -n 30
```

Restart the bot:
```bash
systemctl restart claw-trading
```

## Trading Philosophy

SharpClaw is designed for **low win rate (~30%), high reward-to-risk ratio (4-5x)**. This is intentional:
- Frequent small losses are expected and normal
- The system profits from rare large gains (fat-tailed meme-coin returns)
- Circuit breaker triggers on cumulative P&L, not win/loss count
- Each strategy self-evolves: winning strategies get amplified, losing ones get attenuated or auto-disabled

## Safety Rules

1. **Never bypass the circuit breaker** - if it's triggered, wait for cooldown
2. **Always check status before manual trades** - verify exposure limits
3. **Use analyze before manual buys** - the rug check can save from scams
4. **Maximum position size** is enforced automatically (default 0.5 SOL)
5. **The bot manages stop-losses automatically** - don't manually sell positions that have active stops unless the user explicitly requests it

## Interpreting Results

- **Sentiment score**: -100 (very bearish) to +100 (very bullish). Source shown as "Grok" (xAI) or "DexScreener" (synthetic)
- **Hype score**: 0-100 composite of volume, momentum, buy count, liquidity
- **Rug score**: 0-100 from RugCheck API. Lower is safer. >50 = high risk
- **Strategy expectancy**: positive = strategy has edge, negative = losing. Auto-disabled at z < -1.65 after 20+ trades
- **Confidence scale**: 0.3x-1.5x multiplier on position size based on historical performance

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/garfeildma) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
