---
name: futu
description: Futu OpenAPI integration for stock trading and market data via FutuOpenD. Use this skill when users need to: (1) Submit, cancel, or modify stock orders (HK/US/CN markets), (2) Query account balance, stock positions, or margin info, (3) Get real-time quotes, candlestick data, or order book depth, (4) Subscribe to real-time market data push, (5) Query trading days or market sessions. Supports Hong Kong, US, and China A-share markets. Requires local FutuOpenD gateway running. Use when this capability is needed.
metadata:
  author: chuangshizhiqiang
---

# Futu OpenAPI Skill

This skill provides integration with Futu OpenAPI for stock trading and market data access via the FutuOpenD gateway.

## Architecture Overview

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│   Your App      │ ---> │   FutuOpenD     │ ---> │  Futu Server    │
│  (Go Client)    │ TCP  │   (Gateway)     │ TLS  │  (Backend)      │
└─────────────────┘      └─────────────────┘      └─────────────────┘
```

Unlike direct API brokers, Futu requires the FutuOpenD gateway to be running locally or on a server.

## Prerequisites

### 1. Install FutuOpenD

Download FutuOpenD from [Futu OpenAPI](https://openapi.futunn.com/futu-api-doc/):

- macOS: Download .dmg and install
- Windows: Download .exe installer
- Linux: Download .tar.gz

### 2. Configure FutuOpenD

Edit the `FutuOpenD.xml` configuration:

```xml
<FutuOpenD>
    <login_account>your_futu_account</login_account>
    <login_pwd_md5>md5_of_password</login_pwd_md5>
    <ip>127.0.0.1</ip>
    <port>11111</port>
</FutuOpenD>
```

### 3. Start FutuOpenD

```bash
./FutuOpenD
```

## Quick Start

### Basic Usage (Go)

```go
import (
    "context"
    "fmt"
    "log"

    "github.com/hyperjiang/futu"
)

func main() {
    // Connect to FutuOpenD gateway
    sdk, err := futu.NewSDK(
        futu.WithHost("127.0.0.1"),
        futu.WithPort("11111"),
    )
    if err != nil {
        log.Fatal(err)
    }
    defer sdk.Close()

    ctx := context.Background()

    // Get real-time quotes
    quotes, err := sdk.GetBasicQotWithContext(ctx, []string{"HK.00700", "US.AAPL"})
    if err != nil {
        log.Fatal(err)
    }

    for _, q := range quotes {
        fmt.Printf("%s: %.2f\n", q.Code, q.LastPrice)
    }
}
```

## API Categories

### Trading APIs

Submit and manage orders:

```go
// Get trading header
header := &trdcommon.TrdHeader{
    AccID:     &accID,
    TrdEnv:    &trdEnv,    // TrdEnv_Real or TrdEnv_Simulate
    TrdMarket: &trdMarket, // TrdMarket_HK, TrdMarket_US, etc.
}

// Place order
result, err := sdk.PlaceOrderWithContext(ctx, header,
    trdcommon.TrdSide_TrdSide_Buy,
    trdcommon.OrderType_OrderType_Normal,
    "HK.00700",
    100,   // quantity
    350.0, // price
)

// Cancel order
sdk.ModifyOrderWithContext(ctx, header, orderID, 1, 0, 0) // action=1 for cancel

// Modify order
sdk.ModifyOrderWithContext(ctx, header, orderID, 2, newQty, newPrice) // action=2 for modify
```

### Account APIs

Query account information:

```go
// Get account list
accs, err := sdk.GetAccListWithContext(ctx)

// Get funds (balance)
funds, err := sdk.GetFundsWithContext(ctx, header)
fmt.Printf("Cash: %.2f, Total: %.2f\n", funds.Cash, funds.TotalAssets)

// Get positions
positions, err := sdk.GetPositionListWithContext(ctx, header)
for _, p := range positions {
    fmt.Printf("%s: %d shares @ %.2f\n", p.Code, p.Qty, p.Price)
}

// Get max buying power
maxQtys, err := sdk.GetMaxTrdQtysWithContext(ctx, header, "HK.00700")
```

### Quote APIs

Real-time and historical market data:

```go
// Get quotes
quotes, err := sdk.GetBasicQotWithContext(ctx, []string{"HK.00700"})

// Get order book depth
orderBook, err := sdk.GetOrderBookWithContext(ctx, "HK.00700")

// Get K-line data
klines, err := sdk.GetKLWithContext(ctx, "HK.00700",
    qotcommon.KLType_KLType_Day,
    adapt.WithCount(100),
)

// Get recent trades (tickers)
tickers, err := sdk.GetTickerWithContext(ctx, "HK.00700", adapt.WithCount(50))

// Get trading days
days, err := sdk.GetTradeDateWithContext(ctx,
    qotcommon.QotMarket_QotMarket_HK_Security,
    "2024-01-01",
    "2024-12-31",
)
```

### Subscription APIs

Real-time data push:

```go
// Subscribe to quotes
err := sdk.SubscribeWithContext(ctx,
    []string{"HK.00700"},
    []int32{
        int32(qotcommon.SubType_SubType_Basic),
        int32(qotcommon.SubType_SubType_OrderBook),
    },
)

// Unsubscribe
err := sdk.UnsubscribeWithContext(ctx, []string{"HK.00700"}, subTypes)
```

## Symbol Format

Futu uses `{market}.{ticker}` format:

| Market | Example | Description |
|--------|---------|-------------|
| HK | `HK.00700` | Hong Kong stocks |
| US | `US.AAPL` | US stocks |
| SH | `SH.600519` | Shanghai A-shares |
| SZ | `SZ.000001` | Shenzhen A-shares |

## Trading Environments

| Environment | Description |
|-------------|-------------|
| `TrdEnv_Real` | Real money trading |
| `TrdEnv_Simulate` | Paper trading / simulation |

## Error Handling

```go
result, err := sdk.PlaceOrderWithContext(ctx, header, ...)
if err != nil {
    // Check error type and handle appropriately
    log.Printf("Order failed: %v", err)
    return
}
```

## Unified Interface Adapter

This module provides a unified adapter implementing the `BrokerAdapter` interface.

### Using the Unified Adapter

```go
import (
    "openstockskills/common/interfaces"
    "openstockskills/futu/adapter"
)

// Create adapter through factory
factory := adapter.NewFactory()
config := &interfaces.BrokerConfig{
    Type:        interfaces.BrokerFutu,
    Region:      "hk", // or "us", "cn"
    Environment: "simulate", // or "real"
    Extra: map[string]string{
        "host": "127.0.0.1",
        "port": "11111",
        "account_id": "your_account_id", // optional
    },
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
quotes, _ := brokerAdapter.Quote().GetQuote(ctx, []string{"HK.00700"})
balance, _ := brokerAdapter.Account().GetBalance(ctx, "HKD")
positions, _ := brokerAdapter.Account().GetPositions(ctx, nil)

// Submit order through unified interface
resp, err := brokerAdapter.Trade().SubmitOrder(ctx, &models.SubmitOrderRequest{
    Symbol:    "HK.00700",
    Side:      models.OrderSideBuy,
    OrderType: models.OrderTypeLimit,
    Quantity:  100,
    Price:     350.00,
})
```

### Adapter Files

```
futu/
├── adapter/
│   ├── adapter.go    # Main adapter implementing BrokerAdapter
│   ├── trade.go      # TradeService implementation
│   ├── quote.go      # QuoteService implementation
│   ├── account.go    # AccountService implementation
│   ├── converter.go  # Type converters (Futu <-> Unified)
│   ├── errors.go     # Error mapping to unified error codes
│   └── factory.go    # Adapter factory
└── SKILL.md          # This file
```

## Rate Limits

- Quote subscriptions are limited by account tier
- Trade API requests are rate limited
- Use batch operations where possible

## Security Notes

1. **FutuOpenD Gateway**: Keep the gateway secure; it handles authentication
2. **Network**: FutuOpenD should run on localhost or secure internal network
3. **RSA Encryption**: Optional but recommended for sensitive operations

```go
// Enable RSA encryption
sdk, err := futu.NewSDK(
    futu.WithHost("127.0.0.1"),
    futu.WithPort("11111"),
    futu.WithRSAPrivateKey("path/to/private_key.pem"),
)
```

## Differences from Longbridge

| Aspect | Futu | Longbridge |
|--------|------|------------|
| Architecture | Local gateway (FutuOpenD) | Direct API |
| Authentication | Via FutuOpenD | API Key/Token |
| Protocol | TCP + Protobuf | WebSocket + Protobuf |
| SDK | github.com/hyperjiang/futu | github.com/longportapp/openapi-go |

See [../common/SKILL.md](../common/SKILL.md) for the unified interface specification.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chuangshizhiqiang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
