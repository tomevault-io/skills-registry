---
name: netsuite
description: Query NetSuite ERP — records, saved searches, and SuiteQL via the REST API. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# NetSuite

Query records and run SuiteQL queries via the NetSuite REST API.

## Environment Variables

- `NETSUITE_ACCOUNT_ID` - Account ID (e.g. `1234567`)
- `NETSUITE_TOKEN_ID` - Token-based auth token ID
- `NETSUITE_TOKEN_SECRET` - Token-based auth token secret

## SuiteQL query

```bash
curl -s -X POST \
  "https://$NETSUITE_ACCOUNT_ID.suitetalk.api.netsuite.com/services/rest/query/v1/suiteql" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $NETSUITE_TOKEN_SECRET" \
  -H "prefer: transient" \
  -d '{"q":"SELECT id, companyname, email FROM customer WHERE rownum <= 10"}' | jq '.items[]'
```

## List invoices

```bash
curl -s -X POST \
  "https://$NETSUITE_ACCOUNT_ID.suitetalk.api.netsuite.com/services/rest/query/v1/suiteql" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $NETSUITE_TOKEN_SECRET" \
  -H "prefer: transient" \
  -d '{"q":"SELECT id, tranid, entity, total, status FROM transaction WHERE type = '\''CustInvc'\'' AND rownum <= 10"}' | jq '.items[]'
```

## Get record

```bash
curl -s \
  "https://$NETSUITE_ACCOUNT_ID.suitetalk.api.netsuite.com/services/rest/record/v1/customer/CUSTOMER_ID" \
  -H "Authorization: Bearer $NETSUITE_TOKEN_SECRET" | jq '{id, companyName, email, phone}'
```

## Notes

- Token-based authentication used. OAuth 1.0 may be required for some setups.
- Always confirm before creating or modifying records.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
