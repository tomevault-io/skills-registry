---
name: react-native-security
description: Secure storage, network traffic, and deep links in React Native mobile apps. Use when implementing secure storage, certificate pinning, or deep link validation in React Native. Use when this capability is needed.
metadata:
  author: HoangNguyen0403
---
# React Native Security

## **Priority: P0 (CRITICAL)**

## Store Credentials Securely

- **Keychain/Keystore**: Use `react-native-keychain` for tokens, passwords.
- **Never AsyncStorage**: Not encrypted. Only for non-sensitive data.
- **Biometric Auth**: Use `react-native-biometrics` for Face ID/Touch ID.

See [keychain usage reference](references/keychain-usage.md) for Keychain storage with biometric access control.

## Validate Deep Links

- **Validate URLs**: Check scheme and host before navigation.
- **Sanitize Params**: Never trust URL params. Validate and sanitize.
- **Token Extraction**: Avoid passing tokens in deep link URLs. Use secure code exchange.

See [keychain usage reference](references/keychain-usage.md) for deep link URL validation with scheme and host whitelisting.

## Enforce Network Security

- **HTTPS Only**: Enforce via `NSAppTransportSecurity` (iOS) and `network_security_config.xml` (Android).
- **Certificate Pinning**: Use `react-native-ssl-pinning` for high-security apps (banking, healthcare). **Warning**: Requires app update when certificates rotate.
- **No Secrets in Code**: Use `.env` files with `react-native-config`. Add to `.gitignore`.
- **Verify**: Test by attempting plain HTTP requests in dev; confirm they rejected.

## Protect Sensitive Data

- **PII Masking**: Mask email/phone in logs and analytics.
- **Clipboard**: Clear sensitive data after paste.
- **Screenshots**: Block on sensitive screens with `react-native-screen-guard`.
- **Hermes**: Bytecode harder to reverse-engineer. **ProGuard/R8**: Enable on Android.

## Anti-Patterns

- **No Hardcoded Secrets**: Use environment variables.
- **No Sensitive Logs**: Strip `console.log` in production.
- **No Plain HTTP**: Always use HTTPS.
- **No Client-Side Auth**: Validate on backend.

## References

See [references/keychain-usage.md](references/keychain-usage.md) for Keychain, Biometrics, SSL Pinning, and PII Masking.

---
> Source: [HoangNguyen0403/agent-skills-standard](https://github.com/HoangNguyen0403/agent-skills-standard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
