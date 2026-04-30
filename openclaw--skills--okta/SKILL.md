---
name: okta
description: Manage users, groups, and applications via Okta API. Handle identity and access management. Use when this capability is needed.
metadata:
  author: openclaw
---
# Okta
Enterprise identity management.
## Environment
```bash
export OKTA_DOMAIN="your-org.okta.com"
export OKTA_API_TOKEN="xxxxxxxxxx"
```
## List Users
```bash
curl "https://$OKTA_DOMAIN/api/v1/users" \
  -H "Authorization: SSWS $OKTA_API_TOKEN"
```
## Create User
```bash
curl -X POST "https://$OKTA_DOMAIN/api/v1/users?activate=true" \
  -H "Authorization: SSWS $OKTA_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"profile": {"firstName": "John", "lastName": "Doe", "email": "john@example.com", "login": "john@example.com"}}'
```
## List Groups
```bash
curl "https://$OKTA_DOMAIN/api/v1/groups" -H "Authorization: SSWS $OKTA_API_TOKEN"
```
## List Applications
```bash
curl "https://$OKTA_DOMAIN/api/v1/apps" -H "Authorization: SSWS $OKTA_API_TOKEN"
```
## Links
- Admin: https://your-org-admin.okta.com
- Docs: https://developer.okta.com/docs/reference/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
