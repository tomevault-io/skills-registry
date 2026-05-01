---
name: crypto-backtest
description: Crypto futures backtesting engine with built-in EMA, RSI, MACD, and Bollinger Band strategies. Fetches OHLCV data from any ccxt-supported exchange (Bybit, Binance, OKX, etc.), runs multi-strategy sweeps, calculates win rate / PnL / drawdown, and exports results to JSON. Use when backtesting trading strategies, comparing parameter combinations, evaluating crypto trading signals, or building a quantitative trading pipeline. Use when this capability is needed.
metadata:
  author: openclaw
---

# Crypto Backtest Engine

Fast, scriptable backtesting for crypto futures strategies. Fetches data via ccxt, runs strategies, reports metrics.

## Quick Start

```bash
pip install ccxt numpy
python scripts/backtest_engine.py --symbol ETH/USDT:USDT --strategy ema --fast 12 --slow 26
```

## Features

- **Multi-exchange**: Any ccxt-supported exchange (Bybit, Binance, OKX, Bitget...)
- **Built-in strategies**: EMA crossover, RSI, MACD, Bollinger Bands
- **Parameter sweep**: Test all combinations automatically
- **Risk simulation**: Configurable leverage, position size, SL/TP, fees
- **JSON export**: Machine-readable results for pipeline integration
- **Custom strategies**: Simple plug-in interface

## Usage

### Single Strategy
```bash
python scripts/backtest_engine.py \
  --symbol SOL/USDT:USDT \
  --strategy rsi \
  --period 14 --oversold 30 --overbought 70 \
  --capital 1000 --leverage 5
```

### Parameter Sweep
```bash
python scripts/sweep.py \
  --symbol ETH/USDT:USDT \
  --strategies ema,rsi,macd,bbands \
  --capital 1000 --leverage 5 \
  --output results.json
```

### Custom Strategy
See `references/custom_strategy.md` for the plug-in interface.

## Output Metrics

Each backtest reports:
- Total trades, win rate, profit factor
- Total PnL (absolute + percentage)
- Max drawdown
- Best/worst trade
- Final balance

## Files

- `scripts/backtest_engine.py` — Core engine with EMA, RSI, MACD, Bollinger Bands
- `scripts/sweep.py` — Multi-strategy parameter sweep runner
- `references/custom_strategy.md` — Guide for adding custom strategies
- `references/strategy_notes.md` — Notes on each built-in strategy's edge cases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
