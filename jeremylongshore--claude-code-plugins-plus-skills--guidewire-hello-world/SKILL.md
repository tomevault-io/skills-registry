---
name: guidewire-hello-world
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Guidewire Hello World

## Overview

Execute your first Cloud API calls to PolicyCenter, ClaimCenter, and BillingCenter. All Guidewire Cloud APIs are RESTful with JSON payloads and follow Swagger 2.0.

## Instructions

### Step 1: Query PolicyCenter Accounts

```typescript
const token = await getGuidewireToken();
const headers = { 'Authorization': `Bearer ${token}`, 'Content-Type': 'application/json' };

// List accounts
const accounts = await fetch(`${process.env.GW_PC_URL}/account/v1/accounts?pageSize=5`, { headers });
const data = await accounts.json();
data.data.forEach((acct: any) => {
  console.log(`Account: ${acct.attributes.accountNumber} | ${acct.attributes.accountHolderContact.displayName}`);
});
```

### Step 2: Query ClaimCenter Claims

```typescript
// List recent claims
const claims = await fetch(`${process.env.GW_CC_URL}/claim/v1/claims?pageSize=5`, { headers });
const claimData = await claims.json();
claimData.data.forEach((claim: any) => {
  console.log(`Claim: ${claim.attributes.claimNumber} | ${claim.attributes.status.code} | ${claim.attributes.lossDate}`);
});
```

### Step 3: Guidewire API Response Structure

```json
{
  "count": 42,
  "data": [
    {
      "attributes": { "accountNumber": "A000001", "...": "..." },
      "checksum": "abc123",
      "links": { "self": { "href": "/account/v1/accounts/pc:123" } }
    }
  ],
  "links": { "next": { "href": "/account/v1/accounts?pageSize=5&offsetToken=..." } }
}
```

Key patterns: `data[]` array, `attributes` for fields, `checksum` for optimistic locking, `links` for pagination.

## Error Handling

| Error | Code | Solution |
|-------|------|----------|
| `404 Not Found` | Invalid endpoint path | Verify `/account/v1/accounts` format |
| `400 Bad Request` | Invalid query params | Check `pageSize`, `filter` syntax |
| `422 Unprocessable` | Business rule violation | Read `userMessage` in response |
| `409 Conflict` | Stale checksum | Re-GET resource, use new checksum |

For detailed implementation, see: [implementation guide](references/implementation-guide.md)

## Resources

- [PolicyCenter API Reference](https://docs.guidewire.com/cloud/pc/202503/apiref/)
- [ClaimCenter API Reference](https://docs.guidewire.com/cloud/cc/202407/apiref/)
- [Cloud API Developer Guide](https://docs.guidewire.com/cloud/pc/202503/cloudapica/pdf/CloudAPI-Developer.pdf)

## Next Steps

For local development workflow, see `guidewire-local-dev-loop`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
