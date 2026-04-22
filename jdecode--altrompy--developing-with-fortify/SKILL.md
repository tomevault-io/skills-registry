---
name: developing-with-fortify
description: Laravel Fortify headless authentication backend development (login, register, 2FA, etc). Use when this capability is needed.
metadata:
  author: jdecode
---

# Fortify
Headless auth backend.

## Documentation
Use `search-docs` for patterns.

## Usage
- **Routes**: `list-routes` (action: "Fortify", only_vendor: true).
- **Actions**: `app/Actions/Fortify/`.
- **Config**: `config/fortify.php`.
- **Views**: Set in `FortifyServiceProvider::boot()`.

## Features
Enable in `config/fortify.php`:
- `registration()`, `resetPasswords()`, `emailVerification()`, `updateProfileInformation()`, `updatePasswords()`, `twoFactorAuthentication()`.

## Workflows
- **2FA**: Add `TwoFactorAuthenticatable` to User. Enable in config. Run migrations. View callbacks.
- **Verification**: `MustVerifyEmail` on User.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jdecode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
