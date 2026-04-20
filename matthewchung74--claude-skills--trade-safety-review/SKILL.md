---
name: trade-safety-review
description: Reviews trading code for safety invariants and code reuse. Use when modifying trading logic, orders, positions, sizing, or adding features that interact with IBKR. Use when this capability is needed.
metadata:
  author: matthewchung74
---

# Trade Safety Review & Code Reuse Skill

This skill enforces trade safety invariants and code reuse patterns for the warrior trading system.

---

## Part 1: Code Reuse - Search Before Writing

Before implementing ANY new logic, search these locations for existing code:

### Backend Logic (ibkr-scanner-core)

| Category | Location | Existing Functions |
|----------|----------|-------------------|
| Filters | `ibkr-scanner-core/ibkr_scanner_core/logic/filters.py` | `pct_change()`, `passes_price_filter()`, `passes_pct_change_filter()`, `passes_float_filter()`, `passes_rvol_filter()`, `passes_numeric_filters()`, `news_is_recent()`, `is_market_hours()` |
| Trading | `ibkr-scanner-core/ibkr_scanner_core/logic/trading.py` | `trade_windows()`, `risk_size()`, `TradeLimitTracker`, `parse_time()` |
| Models | `ibkr-scanner-core/ibkr_scanner_core/models/` | `Candidate`, `NewsItem`, `CatalystVerdict`, `WatchlistItem`, `SignalEvent`, `DecisionSnapshot` |

### Pattern Detection (candle-patterns)

| Category | Location | Contents |
|----------|----------|----------|
| Indicators | `candle-patterns/candle_patterns/indicators/` | `ema.py`, `macd.py`, `rvol.py`, `vwap.py`, `trend_confirmation.py` |
| Patterns | `candle-patterns/candle_patterns/` | `bull_flag.py`, `micro_pullback.py`, `vwap_break.py`, `opening_range_retest.py`, `base.py` |
| Fixtures | `candle-patterns/tests/fixtures/` | Test data for patterns and exit signals |

### Search Commands to Run

Before writing new logic, run these searches:

```bash
# Search for function name or similar logic
grep -r "function_name_or_keyword" ibkr-scanner-core/ibkr_scanner_core/
grep -r "function_name_or_keyword" candle-patterns/candle_patterns/

# Search for similar calculations
grep -r "pct_change\|percent\|change" ibkr-scanner-core/
grep -r "vwap\|rvol\|ema\|macd" candle-patterns/
```

### Rule

If similar logic exists:
1. Import and use it directly
2. Or extend/modify the existing function
3. NEVER duplicate - creates maintenance burden and bugs

---

## Part 2: Trade Safety Invariants

### Account Configuration

```yaml
# UPDATE THESE VALUES WHEN CHANGING ACCOUNTS
account_type: paper  # paper | live
max_loss_per_trade: 50.00  # USD
max_trades_per_day: 3
flatten_time: "15:50"  # ET
entry_window_start: "09:30"  # ET
entry_window_end: "11:00"  # ET
```

### Safety Checklist for Trading Code Changes

When modifying ANY of these files, verify ALL invariants:

**Files requiring safety review:**
- `ibkr-scanner-core/ibkr_scanner_core/logic/trading.py`
- `ibkr-scanner-core/ibkr_scanner_core/trading/trade_gate.py` (when created)
- `ibkr-scanner-core/ibkr_scanner_core/trading/order_manager.py` (when created)
- `ibkr-scanner-core/ibkr_scanner_core/orchestrator.py` (when created)
- Any file with `order`, `trade`, `position`, or `bracket` in the name

### Invariants to Verify

#### 1. Paper Mode Default
```python
# MUST default to True
paper_mode: bool = True
```
- [ ] Paper mode defaults to `True`
- [ ] Switching to live requires explicit user action
- [ ] Live mode has a warning/confirmation

#### 2. Auto-Trade Default
```python
# MUST default to False
auto_trade_enabled: bool = False
```
- [ ] Auto-trading defaults to `False`
- [ ] Enabling requires explicit user action

#### 3. Position Sizing
```python
# MUST use risk_size() from logic/trading.py
max_loss: float = 50.0  # Never exceed this
```
- [ ] Uses `risk_size()` function, not custom calculation
- [ ] `max_loss` parameter respected (currently $50)
- [ ] Shares rounded DOWN (never risk more than max_loss)

#### 4. Trade Limit
```python
# MUST use TradeLimitTracker from logic/trading.py
max_trades_per_day: int = 3
```
- [ ] Uses `TradeLimitTracker` class
- [ ] Limit checked BEFORE placing order
- [ ] Counter increments AFTER successful fill (not on order placement)

#### 5. Entry Window
```python
# MUST use trade_windows() from logic/trading.py
entry_start: str = "09:30"  # ET
entry_end: str = "11:00"    # ET
```
- [ ] Uses `trade_windows()` function
- [ ] Entry blocked outside window
- [ ] Times are in Eastern Time

#### 6. Flatten Time
```python
flatten_time: str = "15:50"  # ET
```
- [ ] Flatten triggered at or after this time
- [ ] Flatten cancels ALL open orders
- [ ] Flatten closes ALL positions
- [ ] Flatten is non-negotiable (no "hold overnight" option)

#### 7. Order Rejection Handling
- [ ] Rejected orders are logged
- [ ] Rejected orders increment failure counter
- [ ] NO automatic retry of rejected orders

#### 8. IBKR Disconnect Handling
- [ ] Auto-reconnect with exponential backoff
- [ ] Max backoff capped at 60 seconds
- [ ] No trades placed during disconnect

---

## Part 3: Review Workflow

When reviewing trading-related changes:

### Step 1: Identify Scope
List all files modified that touch trading logic.

### Step 2: Check Code Reuse
For each new function:
- Did we search for existing implementations?
- Is there duplicate logic?

### Step 3: Safety Audit
For each safety invariant:
- Is it preserved?
- Is it tested?
- Can it be bypassed?

### Step 4: Test Coverage
- [ ] Unit tests for new logic
- [ ] Boundary tests (edge cases)
- [ ] Integration probe if IBKR-dependent

---

## Part 4: Quick Reference

### Importing Existing Logic

```python
# Filters
from ibkr_scanner_core.logic.filters import (
    pct_change,
    passes_numeric_filters,
    news_is_recent,
    is_market_hours,
)

# Trading
from ibkr_scanner_core.logic.trading import (
    trade_windows,
    risk_size,
    TradeLimitTracker,
)

# Indicators
from candle_patterns.indicators.vwap import calculate_vwap
from candle_patterns.indicators.macd import calculate_macd
from candle_patterns.indicators.ema import calculate_ema
from candle_patterns.indicators.rvol import calculate_rvol
```

### Key Defaults That Must Never Change Without Review

| Setting | Default | Why |
|---------|---------|-----|
| `paper_mode` | `True` | Prevents real money loss |
| `auto_trade_enabled` | `False` | Requires explicit opt-in |
| `max_loss_per_trade` | `$50` | Caps single-trade risk |
| `max_trades_per_day` | `3` | Caps daily exposure |
| `flatten_time` | `15:50 ET` | No overnight positions |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matthewchung74) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
