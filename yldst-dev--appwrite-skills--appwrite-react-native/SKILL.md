---
name: appwrite-react-native
description: Appwrite integration for React Native and Expo apps. Use when building mobile auth/data flows with react-native-appwrite, platform registration, session handling, and secure API boundaries. Use when this capability is needed.
metadata:
  author: yldst-dev
---

# Appwrite React Native

Use this skill for React Native mobile apps using Appwrite.

## Verified Baseline

- Quick start uses Expo app creation.
- Required packages include `react-native-appwrite` and `react-native-url-polyfill`.
- Appwrite client setup includes endpoint, project ID, and platform identifier.

## Setup Workflow

1. Create Appwrite project and register mobile platforms in Console.
2. Initialize Expo or React Native app.
3. Install Appwrite mobile SDK packages.
4. Build a centralized Appwrite client module for mobile runtime.
5. Implement account registration, session login, current-user fetch, logout.

## Platform Registration Rules

- Add Android and iOS app identifiers in Appwrite Console before testing auth flows.
- Keep per-environment bundle identifiers and Appwrite projects aligned.
- Validate platform allowlist whenever requests are rejected unexpectedly.

## Auth Pattern

- Use `Account.create`, `createEmailPasswordSession`, `get`, `deleteSession`.
- Rehydrate signed-in user on app launch.
- Build explicit handling for expired or revoked sessions.

## Data Pattern

- Use user-scoped client operations for personal data.
- Route privileged writes through backend or Functions.
- Keep business-critical authorization checks on trusted server boundaries.

## Security Guardrails

- Never embed API keys in mobile apps.
- Keep sensitive state out of logs and crash reports.
- Validate tenant/user ownership server-side for updates and deletes.
- Apply tight row permissions and avoid global write grants.

## Common Pitfalls

- Platform misconfiguration causing unauthorized SDK requests.
- Session state inconsistencies during cold start.
- Permission gaps between table and row level rules.
- Assuming mobile client checks are sufficient for authorization.

## References

- https://appwrite.io/docs/quick-starts/react-native
- https://appwrite.io/docs/references/quick-start
- https://appwrite.io/docs/products/auth
- https://appwrite.io/docs/products/databases/permissions

---
> Source: [yldst-dev/appwrite-skills](https://github.com/yldst-dev/appwrite-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
