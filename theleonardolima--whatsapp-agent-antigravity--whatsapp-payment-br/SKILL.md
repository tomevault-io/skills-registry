---
name: whatsapp-payment-br
description: Handle WhatsApp Payments in Brazil, including PIX, Boleto, and Order Status updates. Use for tasks like "Generate a PIX code for this order" or "Update order status to shipped". Use when this capability is needed.
metadata:
  author: theleonardolima
---

# Whatsapp Payment Specialist (BR)

## Goal
To facilitate seamless payment flows within WhatsApp for the Brazilian market, ensuring correct formatting for PIX and Boleto integrations.

## Capabilities
1.  **Order Creation**: Send `order_details` interactive messages with specific payment settings.
2.  **PIX Integration**: Format `pix_dynamic_code` payloads for bank app copy-paste.
3.  **Boleto Integration**: Format `boleto` payloads with `digitable_line`.
4.  **Status Management**: Update orders to `processing`, `shipped`, or `completed` via `order_status`.

## Workflow

### 1. Initiating an Order
**PIX Example Payload**:
```json
{
  "messaging_product": "whatsapp",
  "to": "<PHONE_NUMBER>",
  "type": "interactive",
  "interactive": {
    "type": "order_details",
    "action": {
      "name": "review_and_pay",
      "parameters": {
        "reference_id": "ORDER_12345",
        "currency": "BRL",
        "total_amount": { "value": 5000, "offset": 100 },
        "payment_settings": [{
          "type": "pix_dynamic_code",
          "pix_dynamic_code": {
            "code": "000201010212268... (PIX STRING)",
            "merchant_name": "My Business",
            "key": "business@email.com",
            "key_type": "EMAIL"
          }
        }]
      }
    }
  }
}
```

### 2. Updating Status
After confirming payment with your PSP:
```json
{
  "messaging_product": "whatsapp",
  "to": "<PHONE_NUMBER>",
  "type": "interactive",
  "interactive": {
    "type": "order_status",
    "action": {
      "name": "review_order",
      "parameters": {
        "reference_id": "ORDER_12345",
        "order": { "status": "completed" }
      }
    }
  }
}
```

## Constraints
- **Currency**: Fixed to `BRL`.
- **Value**: Use `offset: 100` (e.g., 1000 = R$10.00).
- **Reconciliation**: You must use the `reference_id` to match webhook status with your internal system.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theleonardolima) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
