---
name: longbridge
description: Longbridge OpenAPI integration for stock trading and market data. Use this skill when users need to: (1) Submit, cancel, or modify stock orders (HK/US markets), (2) Query account balance, stock positions, or fund positions, (3) Get real-time quotes, candlestick data, or market depth, (4) Access option chain or warrant information, (5) Query trading days or market sessions, (6) Manage watchlist groups, (7) Subscribe to real-time market data push. Supports Hong Kong and US stock markets, options, and warrants. Use when this capability is needed.
metadata:
  author: chuangshizhiqiang
---

# Longbridge OpenAPI Skill

This skill provides integration with Longbridge OpenAPI for stock trading and market data access.

## Quick Start

### Configuration

Set environment variables before using the API:

```bash
export LONGPORT_APP_KEY="your_app_key"
export LONGPORT_APP_SECRET="your_app_secret"
export LONGPORT_ACCESS_TOKEN="your_access_token"
```

### Basic Usage (Go)

```go
import (
    "context"
    "fmt"
    "log"

    "github.com/longportapp/openapi-go/config"
    "github.com/longportapp/openapi-go/trade"
    "github.com/longportapp/openapi-go/quote"
)

func main() {
    // Initialize from environment variables
    // (LONGPORT_APP_KEY, LONGPORT_APP_SECRET, LONGPORT_ACCESS_TOKEN)
    cfg, err := config.New()
    if err != nil {
        log.Fatal(err)
    }

    ctx := context.Background()

    // Trade context for orders and account
    tradeCtx, err := trade.NewFromCfg(cfg)
    if err != nil {
        log.Fatal(err)
    }
    defer tradeCtx.Close()

    // Quote context for market data
    quoteCtx, err := quote.NewFromCfg(cfg)
    if err != nil {
        log.Fatal(err)
    }
    defer quoteCtx.Close()

    // Get quotes
    quotes, err := quoteCtx.Quote(ctx, []string{"AAPL.US"})
    if err != nil {
        log.Fatal(err)
    }
    for _, q := range quotes {
        // LastDone is *decimal.Decimal, use String() to print
        fmt.Printf("%s: %s\n", q.Symbol, q.LastDone.String())
    }
}
```

## API Categories

### Trading APIs

Submit and manage orders, query executions. See [references/trade-api.md](references/trade-api.md) for:

- Order operations: submit, cancel, replace
- Order queries: today/history orders
- Execution queries: today/history trades

### Account APIs

Query account and position information. See [references/trade-api.md](references/trade-api.md) for:

- Account balance and cash info
- Stock positions and fund positions
- Margin ratio and cash flow

### Quote APIs

Real-time and historical market data. See [references/quote-api.md](references/quote-api.md) for:

- Real-time quotes and depth
- Candlestick and intraday data
- Option chain and warrant info
- Trading sessions and days

### Type Definitions

Common types, enums and constants. See [references/types.md](references/types.md) for:

- Order types, sides, and statuses
- Quote periods and adjust types
- Market identifiers

### Complete Examples

Full code examples for common use cases. See [references/examples.md](references/examples.md) for:

- Order management workflows
- Portfolio monitoring with P&L
- Real-time subscription setup
- Options trading patterns

### Error Handling & Adaptation

Error codes, retry patterns, and wrapper design. See [references/error-handling.md](references/error-handling.md) for:

- Common error codes and actions
- Retry with exponential backoff
- Adaptation layer design patterns

### Test Scripts

Validation scripts for API connectivity. See [scripts/README.md](scripts/README.md) for:

- Connection test script (test_connection.go)
- Setup instructions
- Troubleshooting guide

## Symbol Format

Symbols use the format `{ticker}.{market}`:

| Market | Example | Description |
|--------|---------|-------------|
| US | `AAPL.US` | US stocks |
| HK | `700.HK` | Hong Kong stocks |
| SH | `600519.SH` | Shanghai stocks |
| SZ | `000001.SZ` | Shenzhen stocks |

## Common Workflows

### Submit a Limit Order

```go
price, _ := decimal.NewFromString("150.00")
orderId, err := tradeCtx.SubmitOrder(ctx, &trade.SubmitOrder{
    Symbol:            "AAPL.US",
    OrderType:         trade.OrderTypeLO,
    Side:              trade.OrderSideBuy,
    SubmittedPrice:    price,
    SubmittedQuantity: 100,
    TimeInForce:       trade.TimeTypeDay,
})
```

### Get Real-time Quote

```go
quotes, err := quoteCtx.Quote(ctx, []string{"AAPL.US", "700.HK"})
if err != nil {
    log.Fatal(err)
}
for _, q := range quotes {
    // LastDone is *decimal.Decimal
    fmt.Printf("%s: %s\n", q.Symbol, q.LastDone.String())
}
```

### Subscribe to Quote Push

```go
quoteCtx.OnQuote(func(event *quote.PushQuote) {
    fmt.Printf("Quote update: %s @ %s\n", event.Symbol, event.LastDone)
})
quoteCtx.Subscribe(ctx, []string{"AAPL.US"}, []quote.SubType{quote.SubTypeQuote}, true)
```

## Error Handling

All API calls return errors that should be handled:

```go
orders, err := tradeCtx.TodayOrders(ctx, &trade.GetTodayOrders{})
if err != nil {
    // Handle API error
    log.Printf("Failed to get orders: %v", err)
    return
}
```

## Rate Limits

- Quote API: Subject to subscription tier limits
- Trade API: Rate limited per account
- Use batch operations where possible

## Unified Interface Adapter

This module also provides a unified adapter that implements the common `BrokerAdapter` interface, enabling seamless switching between different brokers.

### Using the Unified Adapter

```go
import (
    "openstockskills/common/interfaces"
    "openstockskills/longbridge/adapter"
)

// Create adapter through factory
factory := adapter.NewFactory()
config := &interfaces.BrokerConfig{
    Type:        interfaces.BrokerLongbridge,
    AppKey:      os.Getenv("LONGPORT_APP_KEY"),
    AppSecret:   os.Getenv("LONGPORT_APP_SECRET"),
    AccessToken: os.Getenv("LONGPORT_ACCESS_TOKEN"),
    Region:      "hk", // or "cn", "sg"
}

brokerAdapter, err := factory.Create(config)
if err != nil {
    log.Fatal(err)
}
defer brokerAdapter.Disconnect()

// Connect
if err := brokerAdapter.Connect(ctx); err != nil {
    log.Fatal(err)
}

// Use unified interface - same code works for any broker
quotes, _ := brokerAdapter.Quote().GetQuote(ctx, []string{"AAPL.US"})
balance, _ := brokerAdapter.Account().GetBalance(ctx, "USD")
positions, _ := brokerAdapter.Account().GetPositions(ctx, nil)

// Submit order through unified interface
resp, err := brokerAdapter.Trade().SubmitOrder(ctx, &models.SubmitOrderRequest{
    Symbol:    "AAPL.US",
    Side:      models.OrderSideBuy,
    OrderType: models.OrderTypeLimit,
    Quantity:  100,
    Price:     150.00,
})
```

### Adapter Files

```
longbridge/
├── adapter/
│   ├── adapter.go    # Main adapter implementing BrokerAdapter
│   ├── trade.go      # TradeService implementation
│   ├── quote.go      # QuoteService implementation
│   ├── account.go    # AccountService implementation
│   ├── converter.go  # Type converters (Longbridge <-> Unified)
│   ├── errors.go     # Error mapping to unified error codes
│   └── factory.go    # Adapter factory
```

See [../common/SKILL.md](../common/SKILL.md) for the unified interface specification.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chuangshizhiqiang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
