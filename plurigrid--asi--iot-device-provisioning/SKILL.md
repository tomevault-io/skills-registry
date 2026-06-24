---
name: iot-device-provisioning
description: Secure provisioning for TizenRT/ARTIK IoT devices. Coordinates certificate installation, key management, and network onboarding. Use when this capability is needed.
metadata:
  author: plurigrid
---

# Iot Device Provisioning Skill

**Trit**: 0 (ERGODIC)
**Category**: tizen-iot
**Author**: Tizen Community
**Source**: tizen/iot
**License**: Apache-2.0

## Description

Secure provisioning for TizenRT/ARTIK IoT devices. Coordinates certificate installation, key management, and network onboarding.

## When to Use

This is a Tizen security/IoT skill. Use when:
- Developing Tizen applications (web, native, .NET)
- Auditing Tizen app security
- Provisioning TizenRT/ARTIK IoT devices
- Implementing Tizen compliance
- Analyzing SMACK policies or Cynara access control

## Tizen Security Model

### SMACK (Simplified Mandatory Access Control Kernel)
- Linux kernel 3.12+ mandatory access control
- Process isolation via labels
- Prevent inter-app resource access

### Cynara
- Fast privilege access control service
- Policy-based permission checking
- External agent integration

### KeyManager
- Central secure storage repository
- Password-protected data access
- Certificate and key management

### Tizen Manifest
- Privilege declarations (public, partner, platform)
- App sandboxing configuration
- Resource access specifications

## Related Skills

- manifest-privilege-validator
- smack-policy-auditor
- tizen-cve-scanner
- sandbox-escape-detector
- cynara-policy-checker
- iot-device-provisioning

## References

- Tizen Official Docs: https://docs.tizen.org/
- Samsung Security Manager: https://github.com/Samsung/security-manager
- Samsung Cynara: https://github.com/Samsung/cynara
- TizenRT: https://github.com/Samsung/TizenRT

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plurigrid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
