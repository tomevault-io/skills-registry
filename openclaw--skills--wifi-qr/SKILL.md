---
name: wifi-qr
description: Generate QR code for Wi-Fi credentials Use when this capability is needed.
metadata:
  author: openclaw
---

# Wi-Fi QR

Generate a QR code for Wi-Fi credentials. Scan the QR code with a phone to instantly connect to the network without typing the password.

## Commands

```bash
# Generate a QR code for a Wi-Fi network (defaults to WPA)
wifi-qr "MyNetwork" "mypassword"

# Specify the security type explicitly
wifi-qr "MyNetwork" "mypassword" --type WPA
```

## Install

```bash
sudo dnf install qrencode
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
