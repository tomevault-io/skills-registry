---
name: sendgrid
description: Send emails and manage contacts via the SendGrid API. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# SendGrid

Send transactional and marketing emails.

## Environment Variables

- `SENDGRID_API_KEY` - API key

## Send email

```bash
curl -s -X POST -H "Authorization: Bearer $SENDGRID_API_KEY" \
  -H "Content-Type: application/json" \
  "https://api.sendgrid.com/v3/mail/send" \
  -d '{"personalizations":[{"to":[{"email":"recipient@example.com"}]}],"from":{"email":"sender@example.com"},"subject":"Hello","content":[{"type":"text/plain","value":"Message body"}]}'
```

## List contacts

```bash
curl -s -H "Authorization: Bearer $SENDGRID_API_KEY" \
  "https://api.sendgrid.com/v3/marketing/contacts?page_size=10" | jq '.result[] | {id, email, first_name, last_name}'
```

## Get email stats

```bash
curl -s -H "Authorization: Bearer $SENDGRID_API_KEY" \
  "https://api.sendgrid.com/v3/stats?start_date=2024-01-01&limit=7" | jq '.[] | {date, stats: .stats[0].metrics | {requests, delivered, opens, clicks}}'
```

## Notes

- Always confirm before sending emails.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
