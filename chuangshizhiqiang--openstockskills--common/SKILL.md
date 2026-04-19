---
name: stock-trading-common
description: Unified interface layer for multi-broker stock trading integration. Provides common interfaces, data models, and error codes that enable seamless switching between different brokers (Longbridge, Futu, IBKR, Tiger). Use this skill when designing broker adapters or implementing cross-broker trading strategies. Use when this capability is needed.
metadata:
  author: chuangshizhiqiang
---

# Stock Trading Common Interface

This module defines the unified abstraction layer for multi-broker stock trading integration.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    Your Application                      │
├─────────────────────────────────────────────────────────┤
│                 Unified Interface Layer                  │
│     ┌──────────────┬──────────────┬──────────────┐      │
│     │ TradeService │ QuoteService │AccountService│      │
│     └──────────────┴──────────────┴──────────────┘      │
├─────────────────────────────────────────────────────────┤
│                    Adapter Layer                         │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐   │
│  │Longbridge│ │   Futu   │ │   IBKR   │ │  Tiger   │   │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘   │
└─────────────────────────────────────────────────────────┘
```

## Core Interfaces

### BrokerAdapter

The main interface all broker implementations must satisfy:

```go
type BrokerAdapter interface {
    Type() BrokerType
    Name() string
    IsConnected() bool
    Connect(ctx context.Context) error
    Disconnect() error
    Trade() TradeService
    Quote() QuoteService
    Account() AccountService
}
```

### TradeService

Handles order operations:

```go
type TradeService interface {
    SubmitOrder(ctx context.Context, req *SubmitOrderRequest) (*SubmitOrderResponse, error)
    CancelOrder(ctx context.Context, orderID string) error
    ModifyOrder(ctx context.Context, req *ModifyOrderRequest) error
    GetOrder(ctx context.Context, orderID string) (*Order, error)
    GetTodayOrders(ctx context.Context, filter *OrderFilter) ([]*Order, error)
    // ...
}
```

### QuoteService

Handles market data:

```go
type QuoteService interface {
    GetQuote(ctx context.Context, symbols []string) ([]*Quote, error)
    GetDepth(ctx context.Context, symbol string) (*Depth, error)
    GetCandlesticks(ctx context.Context, req *CandlestickRequest) ([]*Candlestick, error)
    Subscribe(ctx context.Context, symbols []string, types []SubscriptionType) error
    // ...
}
```

### AccountService

Handles account operations:

```go
type AccountService interface {
    GetBalance(ctx context.Context, currency string) ([]*Balance, error)
    GetPositions(ctx context.Context, symbols []string) ([]*Position, error)
    GetMarginRatio(ctx context.Context, symbol string) (*MarginRatio, error)
    // ...
}
```

## Unified Data Models

### Order Model

```go
type Order struct {
    OrderID        string
    Symbol         string
    Side           OrderSide    // BUY, SELL
    OrderType      OrderType    // LIMIT, MARKET, etc.
    Status         OrderStatus  // NEW, FILLED, CANCELLED, etc.
    Quantity       int64
    FilledQuantity int64
    Price          float64
    FilledPrice    float64
    // ...
}
```

### Quote Model

```go
type Quote struct {
    Symbol    string
    LastPrice float64
    Open      float64
    High      float64
    Low       float64
    Volume    int64
    Timestamp time.Time
}
```

### Position Model

```go
type Position struct {
    Symbol       string
    Quantity     int64
    CostPrice    float64
    CurrentPrice float64
    MarketValue  float64
    UnrealizedPL float64
}
```

## Unified Error Codes

See [references/error-codes.md](references/error-codes.md) for complete error code documentation.

| Category | Code Range | Examples |
|----------|------------|----------|
| Auth | 1xxx | TOKEN_EXPIRED, PERMISSION_DENIED |
| Connection | 2xxx | CONNECTION_FAILED, TIMEOUT |
| Order | 3xxx | INSUFFICIENT_FUNDS, ORDER_REJECTED |
| Quote | 4xxx | SYMBOL_NOT_FOUND, NO_MARKET_DATA |
| Account | 5xxx | ACCOUNT_SUSPENDED |
| Rate Limit | 6xxx | RATE_LIMITED |
| Internal | 9xxx | INTERNAL_ERROR |

## Usage Example

```go
// Create broker adapter
config := &BrokerConfig{
    Type:        BrokerLongbridge,
    AppKey:      os.Getenv("APP_KEY"),
    AppSecret:   os.Getenv("APP_SECRET"),
    AccessToken: os.Getenv("ACCESS_TOKEN"),
}

adapter, err := factory.Create(config)
if err != nil {
    log.Fatal(err)
}
defer adapter.Disconnect()

// Connect
if err := adapter.Connect(ctx); err != nil {
    log.Fatal(err)
}

// Use unified interface
quote, _ := adapter.Quote().GetQuote(ctx, []string{"AAPL.US"})
balance, _ := adapter.Account().GetBalance(ctx, "USD")

// Submit order (same code works for any broker)
resp, err := adapter.Trade().SubmitOrder(ctx, &SubmitOrderRequest{
    Symbol:    "AAPL.US",
    Side:      OrderSideBuy,
    OrderType: OrderTypeLimit,
    Quantity:  100,
    Price:     150.00,
})
```

## Implementing a New Broker Adapter

See [references/adapter-guide.md](references/adapter-guide.md) for step-by-step instructions on implementing a new broker adapter.

## Module Structure

```
common/
├── SKILL.md              # This file
├── interfaces/
│   └── broker.go         # Core interface definitions
├── models/
│   ├── order.go          # Order-related models
│   ├── quote.go          # Quote-related models
│   └── account.go        # Account-related models
├── errors/
│   └── errors.go         # Unified error codes
└── references/
    ├── interface-spec.md # Detailed interface specification
    └── error-codes.md    # Error code documentation
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chuangshizhiqiang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
