---
name: uni-utilities
description: | Use when this capability is needed.
metadata:
  author: shockz09
---

# Utilities (uni)

No API keys needed. Fast responses.

## Weather

```bash
uni weather London                      # Current weather
uni weather "New York, US"              # With country
uni weather Tokyo --forecast 3          # 3-day forecast
uni weather London --units fahrenheit   # Fahrenheit
uni weather 40.7128,-74.0060            # Coordinates
```

## Stocks (Yahoo Finance)

```bash
uni stocks aapl                         # Apple stock
uni stocks tsla                         # Tesla
uni stocks btc-usd                      # Bitcoin
uni stocks eth-usd                      # Ethereum
uni stocks info aapl                    # Detailed info
uni stocks history aapl                 # 1 month history
uni stocks history btc-usd -p 1w        # 1 week
uni stocks history tsla -p 1y           # 1 year
uni stocks list                         # Top stocks
uni stocks list crypto                  # Top crypto
uni stocks list indices                 # Major indices
```

## Currency

```bash
uni currency 100 usd to eur             # Convert
uni currency 5000 jpy to usd
uni currency 1000 eur to usd gbp jpy    # Multiple
uni currency --list                     # All currencies
```

## QR Code

```bash
uni qrcode "https://example.com"        # Terminal display
uni qrcode "Hello" --terminal           # ASCII art
uni qrcode "https://..." --output qr.png  # Save file
uni qrcode --wifi "Network:password"    # WiFi QR
uni qrcode "text" --size 512            # Custom size
```

## URL Shortener

```bash
uni shorturl "https://very-long-url.com/path"
uni shorturl "https://is.gd/xxx" --expand  # Expand
uni short "https://example.com"         # Alias
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shockz09) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
