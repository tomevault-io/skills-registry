---
name: invoice-document-type
description: Create an invoice document type with standard fields for invoice processing Use when this capability is needed.
metadata:
  author: nomis-dev
---

# Invoice Document Type Skill

This skill creates a complete invoice document type with all necessary fields for invoice processing and data extraction.

## Fields Definition

The following fields will be created for the Invoice document type:

### Required Fields

- **invoice_number** (text): Unique invoice identifier
- **invoice_date** (date): Date of invoice issuance
- **vendor_name** (text): Name of the vendor/supplier
- **total_amount** (number): Total invoice amount

### Optional Fields

- **due_date** (date): Payment due date
- **vendor_address** (address): Vendor address
- **tax_amount** (number): Tax amount
- **currency** (text): Currency code (e.g., USD, EUR, CNY)
- **payment_terms** (text): Payment terms and conditions
- **line_items** (text): Invoice line items details

## Usage

When this skill is selected:

1. A new document type named "Invoice" (or custom name) will be created
2. All fields listed above will be automatically added to the document type
3. The document type will be ready for document upload and AI extraction

## Field Configuration

```json
{
  "fields": [
    {
      "fieldName": "invoice_number",
      "fieldType": "text",
      "description": "Unique invoice identifier",
      "required": true
    },
    {
      "fieldName": "invoice_date",
      "fieldType": "date",
      "description": "Date of invoice issuance",
      "required": true
    },
    {
      "fieldName": "due_date",
      "fieldType": "date",
      "description": "Payment due date",
      "required": false
    },
    {
      "fieldName": "vendor_name",
      "fieldType": "text",
      "description": "Name of the vendor/supplier",
      "required": true
    },
    {
      "fieldName": "vendor_address",
      "fieldType": "address",
      "description": "Vendor address",
      "required": false
    },
    {
      "fieldName": "total_amount",
      "fieldType": "number",
      "description": "Total invoice amount",
      "required": true
    },
    {
      "fieldName": "tax_amount",
      "fieldType": "number",
      "description": "Tax amount",
      "required": false
    },
    {
      "fieldName": "currency",
      "fieldType": "text",
      "description": "Currency code (e.g., USD, EUR, CNY)",
      "required": false
    },
    {
      "fieldName": "payment_terms",
      "fieldType": "text",
      "description": "Payment terms and conditions",
      "required": false
    },
    {
      "fieldName": "line_items",
      "fieldType": "text",
      "description": "Invoice line items details",
      "required": false
    }
  ]
}
```

## Example

After creating an invoice document type with this skill, you can:

1. Upload invoice documents (PDF, images)
2. Use AI extraction to automatically extract all field values
3. Review and validate extracted data
4. Export or integrate with your accounting system

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nomis-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
