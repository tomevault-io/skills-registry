---
name: guidewire-core-workflow-b
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Guidewire Core Workflow B: Claims Processing

## Overview

Claims lifecycle in ClaimCenter: First Notice of Loss (FNOL), investigation, reserve setting, payment processing, and settlement.

## Claims Lifecycle

```
FNOL -> Investigation -> Reserve -> Payment -> Settlement -> Close
```

## Instructions

### Step 1: Create Claim (FNOL)

```typescript
const claim = await fetch(`${GW_CC}/claim/v1/claims`, {
  method: 'POST', headers,
  body: JSON.stringify({
    data: { attributes: {
      lossDate: '2025-03-15T14:30:00Z',
      lossCause: { code: 'vehcollision' },
      lossType: { code: 'AUTO' },
      policyNumber: 'PA-000001',
      description: 'Rear-end collision at intersection',
      reporter: {
        firstName: 'John', lastName: 'Smith',
        primaryPhone: { phoneNumber: '555-0100' },
      },
    }}
  }),
}).then(r => r.json());
console.log(`Claim created: ${claim.data.attributes.claimNumber}`);
```

### Step 2: Set Reserves

```typescript
await fetch(`${GW_CC}/claim/v1/claims/${claimId}/reserves`, {
  method: 'POST', headers,
  body: JSON.stringify({
    data: { attributes: {
      reserveAmount: { amount: 5000, currency: 'usd' },
      costType: { code: 'claimcost' },
      costCategory: { code: 'body' },
    }}
  }),
});
```

### Step 3: Create Payment

```typescript
await fetch(`${GW_CC}/claim/v1/claims/${claimId}/payments`, {
  method: 'POST', headers,
  body: JSON.stringify({
    data: { attributes: {
      paymentType: { code: 'partial' },
      amount: { amount: 3000, currency: 'usd' },
      payee: { contact: { id: claimantContactId } },
    }}
  }),
});
```

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| `Policy not found` | Invalid policy number | Verify policy exists and is in-force |
| `Reserve exceeds limit` | Authority level exceeded | Escalate to supervisor approval |
| `Payment validation` | Missing payee info | Add contact details before payment |

For detailed implementation, see: [implementation guide](references/implementation-guide.md)

## Resources

- [ClaimCenter Cloud API](https://docs.guidewire.com/cloud/cc/202407/apiref/)

## Next Steps

For common errors, see `guidewire-common-errors`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
