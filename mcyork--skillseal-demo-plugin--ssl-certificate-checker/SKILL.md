---
name: ssl-certificate-checker
description: Check SSL/TLS certificates on remote hosts using openssl s_client Use when this capability is needed.
metadata:
  author: mcyork
---

# SSL Certificate Checker

A skill that teaches an LLM agent how to inspect and evaluate SSL/TLS certificates on remote hosts using `openssl s_client`.

## Purpose

Check the SSL/TLS certificate of any remote host and produce a clear, actionable report covering: certificate validity, expiry, issuer, subject, Subject Alternative Names (SANs), and chain of trust status.

## Prerequisites

- `openssl` must be available on the system PATH (ships with macOS and most Linux distributions)
- Network access to the target host on the specified port (default: 443)

## Usage

When the user asks you to check an SSL certificate, follow the steps below. The user may provide:
- A hostname (e.g., `example.com`)
- A hostname with port (e.g., `example.com:8443`)
- A URL (e.g., `https://example.com/some/path`) -- extract just the hostname

If a URL is provided, extract the hostname. If no port is specified, default to 443.

---

## Step 1: Fetch the Certificate

Run the following command to connect to the host and retrieve the full certificate and chain:

```bash
echo | openssl s_client -connect HOST:PORT -servername HOST 2>/dev/null
```

**Important flags:**
- `-servername HOST` enables SNI (Server Name Indication), required for hosts that serve multiple certificates on the same IP
- `echo |` sends empty input so openssl exits cleanly after handshake
- `2>/dev/null` suppresses stderr diagnostic output

---

## Step 2: Extract and Decode the Certificate

```bash
echo | openssl s_client -connect HOST:PORT -servername HOST 2>/dev/null | \
  openssl x509 -noout -text -dates -issuer -subject -ext subjectAltName
```

---

## Step 3: Check Days Until Expiry

```bash
echo | openssl s_client -connect HOST:PORT -servername HOST 2>/dev/null | \
  openssl x509 -noout -checkend SECONDS
```

Exit code 0 = certificate will NOT expire within that period. Exit code 1 = it WILL.

---

## Step 4: Verify the Chain of Trust

```bash
echo | openssl s_client -connect HOST:PORT -servername HOST 2>&1 | head -20
```

Look for `Verify return code: 0 (ok)` for a valid chain.

---

## Step 5: Produce the Report

```
SSL Certificate Report: HOST:PORT
====================================
Subject:       CN=example.com, O=Example Inc
Issuer:        CN=R11, O=Let's Encrypt, C=US
Valid From:    Jan  1 00:00:00 2026 GMT
Valid Until:   Apr  1 00:00:00 2026 GMT
Days Left:     46
SANs:          example.com, www.example.com
Chain Status:  Valid (Verify return code: 0 (ok))
Overall:       PASS | WARN | FAIL
```

### Decision Logic

- **PASS**: Chain valid, not expired, more than 30 days until expiry
- **WARN**: Chain valid but expires within 30 days
- **FAIL**: Expired, chain failed, self-signed, connection refused, or hostname mismatch

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcyork) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
