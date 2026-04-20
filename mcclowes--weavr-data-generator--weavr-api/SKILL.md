---
name: weavr-api
description: Use when making Weavr API calls for corporates, consumers, cards, accounts, transfers, or KYC/KYB
metadata:
  author: mcclowes
---

# Weavr Multi API

API Reference: https://weavr-multi-api.redoc.ly/

## Quick Start

```typescript
// Create a Corporate - POST /multi/corporates
// Headers: api-key, programme-key, Content-Type: application/json
{
  "rootUser": { "name": "John", "surname": "Smith", "email": "john@acme.com",
    "mobile": { "countryCode": "+44", "number": "7700900123" },
    "companyPosition": "DIRECTOR", "dateOfBirth": { "year": 1985, "month": 6, "day": 15 }
  },
  "company": { "type": "LLC", "name": "Acme Ltd", "registrationNumber": "12345678",
    "registrationCountry": "GB", "businessAddress": { "addressLine1": "123 High St",
    "city": "London", "postCode": "EC1A 1BB", "country": "GB" }
  },
  "baseCurrency": "GBP"
}
```

## Core Endpoints

- **Identity**: `/multi/corporates`, `/multi/consumers`, `/{id}/kyb/start`, `/{id}/kyc/start`
- **Auth**: `/multi/login_with_password`, `/multi/authentication/step-up`
- **Instruments**: `/multi/managed_accounts`, `/multi/managed_cards`
- **Transfers**: `/multi/transfers`, `/multi/sends`, `/multi/outgoing_wire_transfers`

## Authentication

Headers: `api-key` (server), `programme-key` (programme), `Authorization: Bearer` (user)

## Reference Files

See [references/endpoints.md](references/endpoints.md) for full endpoint details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcclowes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
