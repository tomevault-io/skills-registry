---
name: uni-sendgrid
description: | Use when this capability is needed.
metadata:
  author: shockz09
---

# SendGrid (uni)

Send transactional emails from the terminal.

## Authentication

```bash
uni sendgrid auth SG.xxx -f sender@example.com           # Set API key
uni sendgrid auth SG.xxx -f sender@example.com -n "App"  # With from name
uni sendgrid auth --status                                # Check configuration
uni sendgrid auth --logout                                # Remove credentials

# Or use environment variables
export SENDGRID_API_KEY="SG.xxx"
export SENDGRID_FROM="sender@example.com"
```

Get API key from: https://app.sendgrid.com/settings/api_keys

## Send Email

```bash
# Plain text email
uni sendgrid send user@example.com "Subject" "Message body here"

# HTML email
uni sendgrid send user@example.com "Welcome!" --html "<h1>Welcome!</h1><p>Thanks for joining.</p>"

# Multiple recipients
uni sendgrid send "a@test.com,b@test.com" "Newsletter" "Content here"

# With CC/BCC
uni sendgrid send user@example.com "Subject" "Body" --cc manager@example.com
uni sendgrid send user@example.com "Subject" "Body" --bcc archive@example.com

# With dynamic template
uni sendgrid send user@example.com "Welcome" -t d-xxxxx -d '{"name":"John","company":"Acme"}'
```

## Notes

- From email must be a verified sender in SendGrid
- Dynamic templates use handlebars syntax in SendGrid dashboard
- Template IDs start with `d-`
- Multiple recipients separated by commas

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shockz09) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
