---
name: receipt-document-type
description: Create a receipt document type for purchase receipts and transaction records Use when this capability is needed.
metadata:
  author: nomis-dev
---

# Receipt Document Type Skill

This skill creates a receipt document type optimized for retail receipts and purchase records.

## Field Configuration

```json
{
  "fields": [
    {
      "fieldName": "receipt_number",
      "fieldType": "text",
      "description": "Receipt or transaction number",
      "required": true
    },
    {
      "fieldName": "date",
      "fieldType": "date",
      "description": "Purchase date",
      "required": true
    },
    {
      "fieldName": "merchant_name",
      "fieldType": "text",
      "description": "Name of the merchant",
      "required": true
    },
    {
      "fieldName": "merchant_address",
      "fieldType": "address",
      "description": "Merchant location",
      "required": false
    },
    {
      "fieldName": "total_amount",
      "fieldType": "number",
      "description": "Total purchase amount",
      "required": true
    },
    {
      "fieldName": "payment_method",
      "fieldType": "text",
      "description": "Payment method used (cash, card, etc.)",
      "required": false
    },
    {
      "fieldName": "items",
      "fieldType": "text",
      "description": "List of purchased items",
      "required": false
    }
  ]
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nomis-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
