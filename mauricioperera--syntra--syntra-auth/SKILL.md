---
name: syntra-auth
description: Authentication and user management with Syntra. Use when setting up auth, configuring OAuth providers, managing users, handling email verification, password resets, OTP codes, or configuring password policies and session settings. Use when this capability is needed.
metadata:
  author: mauricioperera
---

# Syntra Authentication

## Auth setup workflow

1. `auth_get_config` — check current auth settings
2. `auth_update_config` — configure password rules, session timeout, email verification
3. `auth_upsert_oauth_config` — add OAuth providers (Google, GitHub, Discord, etc.)
4. `auth_register` — create first user
5. `auth_login` — verify login works

## User management

- **Register**: `auth_register` with `email`, `password`, optional `name`
- **Login**: `auth_login` returns access + refresh tokens
- **Admin login**: `auth_admin_login` for project admin access
- **List users**: `auth_list_users` with `limit`/`offset` pagination
- **Find user**: `auth_get_user_by_id` or `auth_get_user_by_email`
- **Update profile**: `auth_update_profile` with `user_id` and key-value `profile` object
- **Delete users**: `auth_delete_users` with array of `user_ids`

## Auth configuration

`auth_update_config` accepts:

| Field | Type | Description |
|---|---|---|
| `require_email_verification` | boolean | Require users to verify email |
| `password_min_length` | number | Minimum password length |
| `require_number` | boolean | Require number in password |
| `require_lowercase` | boolean | Require lowercase letter |
| `require_uppercase` | boolean | Require uppercase letter |
| `require_special_char` | boolean | Require special character |
| `session_timeout` | number | Session timeout in seconds |
| `verify_email_method` | string | Email verification method |
| `reset_password_method` | string | Password reset method |
| `sign_in_redirect_to` | string | Post-sign-in redirect URL |

## Email verification and OTP

1. `auth_generate_otp_code` — generate 6-digit code (for email verification or password reset)
2. Send the code to user via `system_send_email`
3. `auth_verify_otp` — verify the code user provides
4. `auth_mark_email_verified` — manually mark email as verified (admin shortcut)

For magic links, use `auth_generate_otp_token` instead (long-form token for URLs).

## OAuth providers

- `auth_list_oauth_configs` — see configured providers
- `auth_upsert_oauth_config` — create or update a provider
- `auth_delete_oauth_config` — remove a provider

### Configure a provider

```json
{
  "provider": "google",
  "client_id": "your-client-id.apps.googleusercontent.com",
  "client_secret": "your-client-secret",
  "scope": "openid email profile",
  "redirect_url": "http://localhost:7130/api/auth/callback/google",
  "enabled": true
}
```

## Reference docs

- For OAuth provider URLs and scopes: see [oauth-providers.md](references/oauth-providers.md)
- For token types and auth flows: see [token-types.md](references/token-types.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mauricioperera) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
