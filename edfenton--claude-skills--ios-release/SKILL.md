---
name: ios-release
description: App Store release checklist and preparation with archive, validation, and submission guidance. Use when this capability is needed.
metadata:
  author: edfenton
---

## Purpose
Prepare an iOS app for App Store submission with comprehensive pre-release checklist, archive creation, and validation.

## Arguments
- `--check-only` — Run pre-release checklist without creating archive
- `--archive` — Create release archive
- `--validate` — Validate archive against App Store requirements

## Pre-release checklist (always runs)

### Version & Build
- [ ] Version number incremented (CFBundleShortVersionString)
- [ ] Build number incremented (CFBundleVersion)
- [ ] Version matches App Store Connect

### Code signing
- [ ] Production provisioning profile valid
- [ ] Distribution certificate not expiring soon
- [ ] App ID matches bundle identifier

### App Store requirements
- [ ] App icon (1024x1024, no alpha)
- [ ] Launch screen configured
- [ ] Required device capabilities set
- [ ] Privacy manifest (PrivacyInfo.xcprivacy) present
- [ ] Required usage descriptions in Info.plist

### Quality gates
- [ ] Build succeeds on Release configuration
- [ ] All tests pass
- [ ] No SwiftLint errors
- [ ] No compiler warnings (treat as errors)

### Content
- [ ] Debug code removed/disabled
- [ ] Test accounts removed
- [ ] Analytics pointing to production
- [ ] API endpoints pointing to production

### App Store Connect
- [ ] Screenshots uploaded (all required sizes)
- [ ] App description complete
- [ ] Keywords set
- [ ] Privacy policy URL set
- [ ] Support URL set
- [ ] Age rating questionnaire completed

## Archive workflow

1. Run pre-release checklist
2. Clean build folder
3. Archive with Release configuration
4. Validate archive
5. Report results and next steps

## Validation checks
- Bundle ID matches App Store Connect
- Version/build valid
- Required icons present
- Entitlements valid
- No private API usage detected
- Binary size acceptable

## Output
- Checklist results (pass/fail)
- Archive location (if created)
- Validation results
- Next steps for submission

## Reference
For detailed requirements and troubleshooting, see `reference/ios-release-reference.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edfenton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
