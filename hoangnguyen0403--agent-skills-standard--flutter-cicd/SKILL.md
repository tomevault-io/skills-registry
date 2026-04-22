---
name: flutter-cicd
description: Set up Continuous Integration and Deployment pipelines for Flutter apps. Use when setting up CI/CD pipelines, automated testing, or deployment workflows for Flutter. (triggers: .github/workflows/**.yml, fastlane/**, android/fastlane/**, ios/fastlane/**, ci, cd, pipeline, build, deploy, release, action, workflow) Use when this capability is needed.
metadata:
  author: HoangNguyen0403
---
# CI/CD Standards

## **Priority: P1 (HIGH)**


## Core Pipeline Steps

1. **Environment Setup**: Use stable Flutter channel. Cache dependencies (pub, gradle, cocoapods).
2. **Static Analysis**: Enforce `flutter analyze` and `dart format`. Fail on any warning in strict mode.
3. **Testing**: Run unit, widget, and integration tests. Upload coverage reports (e.g., Codecov).
4. **Build**:
 - **Android**: Build App Bundle (`.aab`) for Play Store.
 - **iOS**: Sign and build `.ipa` (requires macOS runner).
5. **Deployment** (CD): Automated upload to TestFlight/Play Console using standard tools (Fastlane, Codemagic).

## Best Practices

- **Timeout Limits**: Always set `timeout-minutes` (e.g., 30m) to save costs on hung jobs.
- **Fail Fast**: Run Analyze/Format _before_ Tests/Builds.
- **Secrets**: Never commit keys. Use GitHub Secrets or secure vaults for `keystore.jks` and `.p8` certs.
- **Versioning**: Automate version bumping based on git tags or semantic version scripts.

## Reference

- [**GitHub Actions Template**](references/github-actions.md) - Standard workflow file.
- [**Advanced Large-Scale Workflow**](references/advanced-workflow.md) - Parallel jobs, Caching, Strict Mode.
- [**Fastlane Standards**](references/fastlane.md) - Automated Signing & Deployment.

## Anti-Patterns

- **No Secrets in Repo**: Store `keystore.jks`, `.p8`, and `.env` in GitHub Secrets
- **No Uncapped Jobs**: Always set `timeout-minutes` (e.g., 30m) to save runner minutes
- **No Manual Versioning**: Automate `pubspec.yaml` versioning via git tags or scripts
- **No Late Analysis**: Run `flutter analyze` before builds/tests for fast failure

## Related Topics

flutter/testing | dart/tooling

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/HoangNguyen0403) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
