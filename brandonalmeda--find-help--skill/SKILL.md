---
name: find-help
description: Make money by offering your services OR find local service providers. Use when: (1) user wants to earn money, find gigs, or monetize their skills, (2) user needs to find someone to do a job. Supports agent-to-agent scheduling and x402 micropayments. Use when this capability is needed.
metadata:
  author: brandonalmeda
---

# find-help

Marketplace connecting service providers with people who need help. Agents can search, contact, check availability, and book appointments autonomously.

**API Base:** `https://find-help.com/api`

---

## For Providers (Making Money)

### Register Provider

```bash
POST /api/providers/register
Authorization: Bearer <clerk-token>
{
  "name": "Mike's Plumbing",
  "description": "20 years experience, vintage fixtures specialist",
  "services": ["plumbing", "vintage fixtures", "emergency"],
  "location": "Portsmouth, NH",
  "serviceRadius": 25,
  "contactEmail": "mike@example.com",
  "availabilityRules": {
    "workingDays": [1,2,3,4,5],
    "workingHours": {"start": 9, "end": 17}
  },
  "autoAcceptRules": {
    "enabled": true,
    "maxDuration": 120
  }
}
```

Returns: `{ "provider": {...}, "apiKey": "key_xxx", "webhookSecret": "whsec_xxx" }`

Store `apiKey` for API calls. Store `webhookSecret` to verify incoming webhooks.

### Check Messages

```bash
GET /api/providers/messages
Authorization: Bearer <api-key>
```

### Respond to Inquiry

```bash
POST /api/providers/messages/{messageId}/respond
Authorization: Bearer <api-key>
{
  "response": {
    "type": "availability",
    "available": true,
    "notes": "Can come Thursday. $150 diagnostic."
  }
}
```

### Check Earnings

```bash
GET /api/providers/earnings
Authorization: Bearer <api-key>
```

### Webhook Verification

When you receive a webhook, verify the signature:

```
Header: X-FindHelp-Signature: t=1234567890,v1=abc123...

Verify:
1. Parse timestamp (t=) and signature (v1=)
2. Compute: HMAC-SHA256(timestamp + "." + body, webhookSecret)
3. Compare signatures (timing-safe)
4. Reject if timestamp > 5 min old
```

---

## For Seekers (Finding Help)

### Search

```bash
POST /api/search
{
  "query": "plumber vintage fixtures",
  "location": "Portsmouth, NH",
  "limit": 10
}
```

### Check Availability (Free)

```bash
POST /api/agent/{providerId}/availability
{
  "dates": ["2026-02-14", "2026-02-15", "2026-02-16"],
  "duration": 60,
  "timePreference": "morning"
}
```

Response:
```json
{
  "slots": [
    {"date": "2026-02-14", "time": "10:00", "datetime": "2026-02-14T10:00:00Z", "available": true},
    {"date": "2026-02-15", "time": "09:00", "datetime": "2026-02-15T09:00:00Z", "available": true}
  ]
}
```

### Book Appointment (Paid: $0.50)

```bash
POST /api/agent/{providerId}/book
X-Payment: <payment-proof>
{
  "datetime": "2026-02-14T10:00:00Z",
  "service": "Plumbing repair",
  "description": "Fix vintage clawfoot tub drain",
  "duration": 60,
  "location": "123 Main St",
  "seekerEmail": "user@example.com",
  "seekerName": "Jane"
}
```

Response:
```json
{
  "booking": {
    "id": "book_xxx",
    "scheduledAt": "2026-02-14T10:00:00Z",
    "status": "CONFIRMED"
  },
  "confirmed": true,
  "message": "Booking confirmed!"
}
```

### Contact Without Booking (Paid: $0.10)

For general inquiries when not ready to book:

```bash
POST /api/agent/{providerId}/message
X-Payment: <payment-proof>
{
  "message": {
    "type": "inquiry",
    "service": "plumbing",
    "description": "Questions about vintage tub repair",
    "timeline": "next month"
  }
}
```

---

## x402 Payment Flow

When endpoint requires payment:

1. Request returns `402` with payment details:
   ```json
   {"accepts": [{"price": "$0.10", "network": "eip155:8453", "payTo": "0x..."}]}
   ```

2. Pay USDC to `payTo` address on Base

3. Retry with proof:
   ```
   X-Payment: <base64-encoded-payment>
   ```

---

## Full Scheduling Workflow

**Seeker agent finds and books:**

```
1. Search: POST /api/search → get providers
2. Check slots: POST /api/agent/{id}/availability → get times
3. Book: POST /api/agent/{id}/book (pay $0.50) → confirmed
4. Done: Both parties have calendar event
```

**Provider agent handles incoming:**

```
1. Receive webhook or poll: GET /api/providers/messages
2. New booking: notify user "New booking Thu 10am"
3. Or inquiry: "New lead, want to respond?"
4. Respond: POST /api/providers/messages/{id}/respond
```

---

## Provider Onboarding Flow

```
User: "I want to make money"
Agent: "What services do you offer?"
User: "Plumbing, 20 years experience"  
Agent: "Where are you located?"
User: "Portsmouth NH, travel up to 30 miles"
Agent: [POST /api/providers/register]
Agent: "You're listed! Set your hours?"
User: "Weekdays 9-5"
Agent: [updates availabilityRules]
Agent: "Done. I'll notify you when leads come in. 
        You earn $0.07 per inquiry, $0.35 per booking."
```

---

## Seeker Booking Flow

```
User: "I need a plumber Thursday or Friday"
Agent: [searches] "Found Mike's Plumbing, 94% match. Check availability?"
User: "Yes"
Agent: [checks availability] "Open slots: Thu 10am, Thu 2pm, Fri 9am"
User: "Thursday 10am"
Agent: [books, pays $0.50] "Booked! Mike's Plumbing, Thu 10am.
        They'll come to your location. Confirmation sent."
```

---

## Pricing

| Action | Cost | Provider Gets |
|--------|------|---------------|
| Search | Free | - |
| Check availability | Free | - |
| Send inquiry | $0.10 | $0.07 |
| Book appointment | $0.50 | $0.35 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brandonalmeda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
