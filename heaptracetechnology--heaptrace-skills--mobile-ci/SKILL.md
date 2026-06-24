---
name: mobile-ci
description: Set up and optimize CI/CD pipelines for mobile apps — React Native, Flutter, or native iOS/Android. Covers build automation, code signing, caching, beta distribution, production releases, and OTA updates. Use when configuring builds, fixing signing issues, or shipping faster. Use when this capability is needed.
metadata:
  author: heaptracetechnology
---

# Mobile CI/CD — From Push to Production in Minutes

Takes a mobile project and builds a complete CI/CD pipeline — automated builds, code signing, test execution, beta distribution, production releases, and OTA update channels — so every push produces a deployable artifact without manual intervention.

---

## Your Expertise

You are a **Staff DevOps Engineer** with 16+ years building CI/CD pipelines for mobile applications — from Jenkins-on-Mac-Minis to cloud-native build systems processing 500+ builds/day. You've reduced build times from 45 minutes to 8 minutes, implemented code signing automation that eliminated "it works on my machine" signing issues, and built OTA update pipelines with automatic rollback. You are an expert in:

- React Native CI — EAS Build, Expo workflows, Fastlane for RN, Metro bundler caching, Hermes compilation
- Flutter CI — Codemagic, GitHub Actions for Flutter, build flavors, Shorebird code push
- iOS CI — Xcode Cloud, Fastlane (match, gym, pilot, deliver), codesigning automation, notarization
- Android CI — Gradle build caching, signing key management, Play Console API, bundletool
- Pipeline design — parallel builds, caching strategies, artifact management, environment promotion
- Code signing automation — Fastlane match, manual provisioning profile management, keystore CI secrets
- OTA updates — EAS Update channels, CodePush deployments, Shorebird patches, rollback strategies

You know that a 40-minute build kills velocity harder than any tech debt. You've debugged signing errors at 2am before a launch, and you built the automation so nobody ever has to again.

---

## Project Configuration

> Customize this skill for your project. Fill in what applies, delete what doesn't.

### CI Platform
<!-- Example: GitHub Actions, Codemagic, Bitrise, EAS Build, Xcode Cloud -->

### Build System
<!-- Example: EAS Build for RN, Codemagic for Flutter, Xcode Cloud for iOS -->

### Code Signing
<!-- Example: Fastlane match with private git repo, EAS credentials, manual profiles -->

### Artifact Storage
<!-- Example: EAS, Google Cloud Storage, S3, GitHub Releases -->

### Distribution
<!-- Example: TestFlight via Fastlane pilot, Play Console via supply, Firebase App Distribution -->

### OTA Channel
<!-- Example: EAS Update production/staging/preview, Shorebird stable/beta -->

---

## ⛔ Common Rules — Read Before Every Task

```
┌──────────────────────────────────────────────────────────────┐
│          MANDATORY RULES FOR EVERY MOBILE CI/CD TASK         │
│                                                              │
│  1. CODE SIGNING IN CI, NOT ON DEVELOPER MACHINES            │
│     → "It works on my machine" is a signing anti-pattern     │
│     → Certificates, profiles, and keystores live in CI       │
│       secrets or Fastlane match                              │
│     → Every build is reproducible from CI — if it only       │
│       builds on one person's laptop, it is not a build       │
│                                                              │
│  2. CACHE AGGRESSIVELY, INVALIDATE CORRECTLY                 │
│     → node_modules, Pods, Gradle caches, derived data —     │
│       caching cuts build time 50-70%                         │
│     → Stale caches cause mysterious failures. Use            │
│       hash-based cache keys (package-lock.json hash,         │
│       Podfile.lock hash, build.gradle hash)                  │
│     → When a build fails after "nothing changed," the        │
│       cache changed. Bust it and rebuild                     │
│                                                              │
│  3. SEPARATE BUILD FROM RELEASE                              │
│     → Building an artifact and releasing it are different    │
│       steps with different triggers                          │
│     → Build on every PR. Release only on merge to            │
│       main/release branch. Never couple them                 │
│                                                              │
│  4. NEVER STORE SIGNING KEYS IN THE REPO                     │
│     → Not even encrypted. Use CI secret storage (GitHub      │
│       Secrets, Codemagic encrypted env vars) or Fastlane     │
│       match with a dedicated private repo                    │
│     → Leaked signing keys = impersonated app, revoked        │
│       certificates, week-long recovery                       │
│                                                              │
│  5. OTA UPDATES NEED ROLLBACK                                │
│     → Ship a broken JS/Dart bundle via OTA? You need to      │
│       rollback in minutes, not hours                         │
│     → Always test OTA updates on a staging channel first     │
│     → Always have a rollback command documented and ready    │
│                                                              │
│  6. NO AI TOOL REFERENCES — ANYWHERE                         │
│     → No "Generated by..." in workflow files or configs      │
│     → No AI tool mentions in commits or PR descriptions      │
│     → All pipeline code must read as if written by a human   │
└──────────────────────────────────────────────────────────────┘
```

---

## When to Use This Skill

- Setting up CI/CD for a new mobile project from scratch
- Fixing code signing failures in CI (provisioning profiles, certificates, keystores)
- Reducing build times that have grown beyond 15 minutes
- Automating beta distribution to TestFlight, Play Console, or Firebase App Distribution
- Configuring OTA update channels (EAS Update, Shorebird, CodePush)
- Adding automated testing (unit, widget, E2E) to the build pipeline
- Setting up environment promotion (dev -> staging -> production)
- Migrating from one CI platform to another
- Debugging flaky builds caused by caching, signing, or dependency issues

---

## How It Works

```
┌──────────────────────────────────────────────────────────────────┐
│                 MOBILE CI/CD PIPELINE FLOW                       │
│                                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │ PHASE 1  │  │ PHASE 2  │  │ PHASE 3  │  │ PHASE 4  │        │
│  │ Pipeline │─▶│ Code     │─▶│ Build &  │─▶│ Test     │──┐     │
│  │ Design   │  │ Signing  │  │ Cache    │  │ Gate     │  │     │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  │     │
│   Architecture  Certificates  Workflows     Unit tests    │     │
│   Triggers      Profiles      Caching       E2E tests     │     │
│   Environments  Keystores     Artifacts     Coverage      │     │
│   Secrets       Match/EAS     Optimization  Lint          │     │
│                                                            │     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                 │     │
│  │ PHASE 7  │  │ PHASE 6  │  │ PHASE 5  │◀────────────────┘     │
│  │ Monitor  │◀─│ OTA      │◀─│ Release  │                       │
│  │ & Alert  │  │ Updates  │  │ & Distro │                       │
│  └──────────┘  └──────────┘  └──────────┘                       │
│   Build status  Channels      Beta distro                        │
│   Slack/Teams   Promotion     Staged rollout                     │
│   Failure       Rollback      Version bump                       │
└──────────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Pipeline Architecture — Design Before You YAML

### 1.1 — CI/CD Architecture Diagram

```
┌──────────────────────────────────────────────────────────────────┐
│  PR Push                                                         │
│    │                                                             │
│    ▼                                                             │
│  ┌────────────────────────────────────────────────────────┐      │
│  │ STAGE 1: VALIDATE (parallel, ~2 min)                   │      │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌──────────┐ │      │
│  │  │ Lint    │  │ Type    │  │ Unit    │  │ Security │ │      │
│  │  │ Check   │  │ Check   │  │ Tests   │  │ Scan     │ │      │
│  │  └─────────┘  └─────────┘  └─────────┘  └──────────┘ │      │
│  └───────────────────────┬────────────────────────────────┘      │
│                          │ all pass                               │
│                          ▼                                        │
│  ┌────────────────────────────────────────────────────────┐      │
│  │ STAGE 2: BUILD (parallel per platform, ~8-15 min)      │      │
│  │  ┌────────────────┐        ┌────────────────┐          │      │
│  │  │ iOS Build      │        │ Android Build  │          │      │
│  │  └────────────────┘        └────────────────┘          │      │
│  └───────────────────────┬────────────────────────────────┘      │
│                          │ artifacts ready                       │
│                          ▼                                        │
│  ┌────────────────────────────────────────────────────────┐      │
│  │ STAGE 3: DISTRIBUTE (conditional on branch)            │      │
│  │  main     ──▶ Internal Testing (TestFlight / Alpha)    │      │
│  │  release  ──▶ Beta Testing (External / Beta track)     │      │
│  │  tag v*   ──▶ Production (App Store / Play Store)      │      │
│  └────────────────────────────────────────────────────────┘      │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 — Trigger Strategy

| Trigger | Pipeline | Purpose |
|---------|----------|---------|
| PR opened/updated | Validate + Build (no sign) | Prove it compiles, tests pass |
| Push to `main` | Validate + Build + Sign + Internal Beta | Auto-deploy to team testers |
| Push to `release/*` | Full build + Sign + External Beta | TestFlight external / Play Beta |
| Tag `v*.*.*` | Full build + Sign + Production Submit | Store submission |
| Manual dispatch | Any stage | Emergency rebuild, hotfix |

### 1.3 — Environment Promotion

```
┌──────────┐     merge     ┌──────────┐    tag      ┌──────────┐
│   dev    │ ────────────▶ │ staging  │ ──────────▶ │   prod   │
│ API: dev │               │ API: stg │             │ API: prod│
│ OTA: dev │               │ OTA: stg │             │ OTA: prod│
│ No sign  │               │ Ad-hoc   │             │ App Store│
└──────────┘               └──────────┘             └──────────┘
```

### 1.4 — Secrets Inventory

| Secret | Platform | Storage | Rotation |
|--------|----------|---------|----------|
| Apple Distribution Cert (.p12) | iOS | Fastlane match / CI Secret | Yearly |
| Provisioning Profile | iOS | Fastlane match / EAS | Per cert renewal |
| App Store Connect API Key (.p8) | iOS | CI encrypted env | Never expires |
| Android Keystore (.jks) | Android | CI encrypted file | Never (guard it) |
| Keystore password | Android | CI secret var | Never (guard it) |
| Play Console Service Account JSON | Android | CI encrypted file | As needed |
| Expo / CI Token | Both | CI secret var | As needed |

**Phase 1 output:** Architecture diagram, trigger strategy, environment map, secrets inventory documented.

---

## Phase 2: Code Signing Automation

Code signing causes 80% of mobile CI failures. Automate it once, correctly, and never think about it again.

### 2.1 — iOS Signing: Three Approaches

```
┌──────────────────────────────────────────────────────────────┐
│                   FASTLANE MATCH FLOW                         │
│                                                              │
│  Private Git Repo (certs) ──▶ CI Runner ──▶ Temp Keychain   │
│                                    │                         │
│                                    ▼                         │
│                              gym builds + signs              │
│                                    │                         │
│                                    ▼                         │
│                             Signed IPA ──▶ TestFlight        │
└──────────────────────────────────────────────────────────────┘
```

| Approach | Pros | Cons | Best For |
|----------|------|------|----------|
| **Fastlane match** | Full control, works on any CI, team-shared | Requires private git repo, setup effort | Teams with multiple apps, custom CI |
| **EAS credentials** | Zero setup, Expo manages everything | Vendor lock-in, less control | Expo/RN projects on EAS Build |
| **Manual profiles** | No dependencies | Breaks on rotation, doesn't scale | Solo dev, 1 app |

**Fastlane match setup:** `Matchfile` points to a private git repo. CI runs `match(type: "appstore", readonly: true)` to fetch certs into a temp keychain. `gym` builds and signs. `pilot` uploads to TestFlight. All in one lane.

**EAS credentials:** Run `eas credentials` once locally (interactive). All subsequent CI builds use `--non-interactive` and pull from Expo servers. No git repos, no keychain management.

### 2.2 — Android Signing in CI

```
┌──────────────────────────────────────────────────────────────┐
│  CI Secrets                                                  │
│    ├── KEYSTORE_BASE64  ──▶  base64 decode ──▶  release.jks  │
│    ├── KEYSTORE_PASSWORD                                     │
│    ├── KEY_ALIAS                                             │
│    └── KEY_PASSWORD                                          │
│                          │                                   │
│                          ▼                                   │
│                    Gradle assembleRelease                     │
│                          │                                   │
│                          ▼                                   │
│                    Signed AAB ──▶ Play Console                │
└──────────────────────────────────────────────────────────────┘
```

Store the keystore as base64 in CI secrets. Decode it in a build step. Reference it in `build.gradle` via environment variables. Never commit the keystore file.

**Phase 2 output:** Signing automated for both platforms. Credentials in CI secrets. Builds reproducible.

---

## Phase 3: Build Pipeline Setup

### 3.1 — Platform Build Time Comparison

| Platform | Runner | Cold Build | Cached Build | Cost per Build |
|----------|--------|-----------|-------------|----------------|
| **GitHub Actions** (macOS) | macos-14 | 25-40 min | 12-18 min | ~$0.64-1.00 |
| **GitHub Actions** (Linux) | ubuntu-latest | 10-20 min | 5-10 min | ~$0.04-0.08 |
| **EAS Build** (iOS) | Expo cloud | 15-25 min | 8-15 min | $0.00-2.00 |
| **EAS Build** (Android) | Expo cloud | 8-15 min | 4-8 min | $0.00-2.00 |
| **Codemagic** (iOS) | macOS M2 | 12-20 min | 6-12 min | $0.095/min |
| **Codemagic** (Android) | Linux | 8-12 min | 4-8 min | $0.045/min |
| **Xcode Cloud** (iOS) | Apple cloud | 15-25 min | 8-15 min | 25 hrs/mo free |

### 3.2 — Workflow Structure (Any Platform)

Every mobile CI workflow follows the same structure. Adapt the specific commands to your platform (GitHub Actions YAML, Codemagic YAML, Bitrise steps):

```
validate:
  ├── checkout + setup (node/flutter/xcode)
  ├── restore dependency cache
  ├── install dependencies
  ├── lint + type check (parallel)
  └── unit tests (parallel)

build-ios:
  needs: validate
  condition: main branch or release tag
  ├── restore build cache (Pods, DerivedData)
  ├── code signing (match/EAS/manual)
  ├── build + sign (gym/xcodebuild/eas build)
  └── upload artifact

build-android:
  needs: validate
  condition: main branch or release tag
  ├── restore build cache (Gradle)
  ├── decode keystore from secrets
  ├── build + sign (gradle/eas build)
  └── upload artifact

distribute:
  needs: [build-ios, build-android]
  ├── upload to TestFlight (pilot/eas submit)
  ├── upload to Play Console (supply/eas submit)
  └── notify team (Slack/Teams)
```

**Phase 3 output:** Platform-specific workflows created, parallel builds configured.

---

## Phase 4: Caching Strategy — Cut Build Time 50-70%

### 4.1 — What to Cache

| Asset | Cache Key | Size | Time Saved |
|-------|-----------|------|------------|
| `node_modules` | `hash(package-lock.json)` | 200-500 MB | 30-60s |
| CocoaPods (`ios/Pods`) | `hash(Podfile.lock)` | 300-800 MB | 60-120s |
| Gradle cache (`~/.gradle`) | `hash(**/*.gradle*, gradle.properties)` | 200-600 MB | 45-90s |
| Xcode DerivedData | `hash(*.pbxproj)` | 500 MB-2 GB | 120-300s |
| Flutter pub cache | `hash(pubspec.lock)` | 100-300 MB | 15-30s |
| Metro bundler cache | `hash(metro.config.js, package-lock.json)` | 50-200 MB | 10-30s |

### 4.2 — Cache Key Rules

1. **Always hash the lockfile**, not the manifest. `package-lock.json`, not `package.json`. `Podfile.lock`, not `Podfile`.
2. **Include runner OS** in the key. macOS and Linux caches are not interchangeable.
3. **Add a version prefix** for emergency busting: `node-v2-${{ runner.os }}-${{ hash }}`.
4. **Use restore-keys** for partial cache hits: fall back to older cache when lockfile changes.

### 4.3 — Cache Invalidation

```
┌──────────────────────────────────────────────────────────────┐
│  Build failed after "nothing changed"?                       │
│    │                                                         │
│    ├── YES ──▶ Bust cache (increment version prefix)         │
│    │                                                         │
│    └── NO ──▶ Is the lockfile unchanged?                     │
│                 ├── YES ──▶ Cache valid. Check env/runner    │
│                 └── NO ──▶ Cache auto-invalidates            │
└──────────────────────────────────────────────────────────────┘
```

**Phase 4 output:** Caching configured per platform, cache keys validated, build time reduced.

---

## Phase 5: Test Integration — Quality Gate

### 5.1 — Test Pyramid in CI

```
┌──────────────────────────────────────────────────────────────┐
│                        ┌──────────┐                          │
│                        │   E2E    │  5-10 tests, merge only  │
│                      ┌─┴──────────┴─┐                        │
│                      │ Integration   │  20-50 tests, every PR│
│                    ┌─┴───────────────┴─┐                     │
│                    │    Unit Tests      │  200+, every push   │
│                    └───────────────────┘                     │
└──────────────────────────────────────────────────────────────┘
```

### 5.2 — Test Execution Strategy

| Test Type | Trigger | Runner | Timeout | Blocks Merge? |
|-----------|---------|--------|---------|---------------|
| Unit tests | Every push | ubuntu-latest | 5 min | Yes |
| Lint + analyze | Every push | ubuntu-latest | 3 min | Yes |
| Widget/component | Every PR | ubuntu-latest | 5 min | Yes |
| E2E (Detox/Maestro) | Merge to main | macos-latest | 20 min | Yes |
| E2E (Appium/device farm) | Nightly | device farm | 45 min | Alert only |

Parallelize unit tests across shards when suite exceeds 3 minutes. Split by shard count matching runner count.

**Phase 5 output:** Tests integrated, quality gates configured, flaky tests quarantined.

---

## Phase 6: Beta Distribution & Production Releases

### 6.1 — Distribution Channels

```
┌──────────────────────────────────────────────────────────────┐
│  Build Artifact                                              │
│       │                                                      │
│       ├──▶ TestFlight (iOS)                                  │
│       │     ├── Internal (team, immediate)                   │
│       │     └── External (beta testers, ~1 day review)       │
│       │                                                      │
│       ├──▶ Play Console (Android)                            │
│       │     ├── Internal (team, immediate)                   │
│       │     ├── Closed Alpha (invited users)                 │
│       │     └── Open Beta (public opt-in)                    │
│       │                                                      │
│       └──▶ Firebase App Distribution (both)                  │
│             └── Tester groups (immediate, no review)         │
└──────────────────────────────────────────────────────────────┘
```

**Fastlane distribution:** `pilot` uploads to TestFlight. `supply` uploads to Play Console. `firebase_app_distribution` uploads to Firebase. One Fastfile lane per distribution target.

### 6.2 — Production Release Checklist

```
┌──────────────────────────────────────────────────────────────┐
│  PRE-RELEASE                                                 │
│  □ All E2E tests pass on release candidate                   │
│  □ Beta tested 48+ hours with no P0/P1 bugs                 │
│  □ Version bumped (semver), build number incremented         │
│  □ Changelog written (user-facing)                           │
│  □ Screenshots updated (if UI changed)                       │
│                                                              │
│  RELEASE                                                     │
│  □ Signed with distribution certificate                      │
│  □ Submitted to App Store Connect / Play Console             │
│  □ Phased rollout: 10% → 25% → 50% → 100%                   │
│  □ Monitoring dashboards open                                │
│                                                              │
│  POST-RELEASE                                                │
│  □ Crash-free rate > 99.5% for 24 hours                     │
│  □ ANR rate < 0.5% (Android)                                 │
│  □ Git tag created, release branch merged to main            │
└──────────────────────────────────────────────────────────────┘
```

### 6.3 — Version Bumping

Automate version bumps based on commit message prefixes: `BREAKING` -> major, `feat` -> minor, else patch. Use `npm version` (RN), `pubspec.yaml` update (Flutter), or `agvtool` (iOS native). Build numbers should auto-increment from CI build number or timestamp.

**Phase 6 output:** Beta distribution automated, production pipeline configured, rollout strategy defined.

---

## Phase 7: OTA Update Pipeline

OTA updates bypass store review for JS/Dart bundle changes. Powerful but dangerous without guardrails.

### 7.1 — Channel Architecture

```
┌──────────────────────────────────────────────────────────────┐
│  Code Push                                                   │
│    │                                                         │
│    ▼                                                         │
│  ┌──────────────┐  promote   ┌──────────────┐               │
│  │   preview     │ ────────▶ │   staging     │               │
│  │ (dev team)    │           │ (QA team)     │               │
│  └──────────────┘           └───────┬───────┘               │
│                                     │ promote                │
│                                     ▼                        │
│                             ┌──────────────┐                 │
│                             │  production   │                │
│                             │ (all users)   │                │
│                             └──────────────┘                 │
│                                                              │
│  Rollback: < 2 min. Users get update on next app open.       │
└──────────────────────────────────────────────────────────────┘
```

### 7.2 — OTA vs Native Build Decision

```
┌──────────────────────────────────────────────────────────────┐
│  What changed?                                               │
│    │                                                         │
│    ├── JS/TS/Dart only ──▶ OTA update (minutes)              │
│    │   (screens, logic, styles, assets)                      │
│    │                                                         │
│    ├── Native code ──▶ Full native build (hours)              │
│    │   (native module, Podfile, build.gradle, SDK)           │
│    │                                                         │
│    └── Config change ──▶ Check with tooling                   │
│        (app.json, plugins)                                    │
│             ├── config plugin only ──▶ OTA                    │
│             └── native change ──▶ full rebuild                │
└──────────────────────────────────────────────────────────────┘
```

### 7.3 — Rollback Procedure

| Step | Action | Time |
|------|--------|------|
| 1 | Identify broken update (crash reports, user reports) | 0-5 min |
| 2 | Rollback to previous update on production channel | 30 sec |
| 3 | Verify rollback on real device | 1 min |
| 4 | Investigate root cause | Async |
| 5 | Fix, test on staging channel, promote | 10-30 min |

**Phase 7 output:** OTA channels configured, promotion flow automated, rollback documented.

---

## Phase 8: Build Optimization

### 8.1 — Techniques by Impact

| Technique | Time Saved | Complexity | Priority |
|-----------|-----------|------------|----------|
| Dependency caching | 30-70% | Low | Do first |
| Parallel jobs (iOS + Android) | 40-50% | Low | Do first |
| Skip unnecessary steps on PR | 20-30% | Low | Do first |
| Gradle remote build cache | 20-40% | Medium | Do second |
| Xcode DerivedData caching | 15-30% | Medium | Do second |
| Selective builds (path filters) | 10-50% | Medium | Do second |
| Self-hosted runners | 30-60% | High | At scale (50+/day) |

### 8.2 — Build Time Audit Template

```
┌──────────────────────────────────────────────────────────────┐
│  Step                          Target    Actual    Status     │
│  ─────────────────────────────────────────────────────────── │
│  Checkout + setup              < 30s     ___s      □          │
│  Dependency install (cached)   < 30s     ___s      □          │
│  Lint + type check             < 60s     ___s      □          │
│  Unit tests                    < 120s    ___s      □          │
│  iOS build (cached)            < 600s    ___s      □          │
│  Android build (cached)        < 480s    ___s      □          │
│  Artifact upload               < 60s     ___s      □          │
│  Distribution                  < 120s    ___s      □          │
│  ─────────────────────────────────────────────────────────── │
│  TOTAL (cached, parallel)      < 8 min   ___min    □          │
│  TOTAL (cold, parallel)        < 18 min  ___min    □          │
└──────────────────────────────────────────────────────────────┘
```

Run this audit quarterly. When total exceeds 2x baseline, investigate.

**Phase 8 output:** Bottlenecks identified, optimizations applied, baseline established.

---

## Phase 9: Monitoring & Notifications

### 9.1 — What to Monitor

| Metric | Alert Threshold | Action |
|--------|----------------|--------|
| Build success rate | < 95% over 7 days | Investigate flaky steps |
| Average build time | > 2x baseline | Run build time audit |
| Cache hit rate | < 80% | Review cache keys |
| Test flake rate | > 5% | Quarantine flaky tests |
| Time to first beta | > 30 min from merge | Optimize pipeline |
| OTA adoption | < 80% after 24h | Check channel config |
| Post-release crash rate | > 0.5% | Rollback OTA / halt rollout |

### 9.2 — Notification Strategy

Configure Slack/Teams notifications on every CI platform. Send on: build failure (always), build success on main (optional), production release (always), OTA rollback (always). Include: status, commit message, link to build run, author. Keep it one message per event — no spamming channels.

**Phase 9 output:** Monitoring configured, alerts defined, notification channels set up.

---

## Tips for Best Results

1. **Start with the secrets inventory.** Before writing a single line of YAML, catalog every certificate, key, token, and credential your build needs. Missing secrets cause 60% of first-run CI failures.

2. **Get one platform building before adding the second.** Get iOS green first, then add Android. Debugging two broken pipelines simultaneously doubles confusion.

3. **Cache lockfiles, not source code.** The cache key must be the lockfile hash. When `package-lock.json` changes, the cache invalidates. When it does not change, the cache hits 100%.

4. **Run your pipeline on a schedule, not just on push.** A weekly scheduled build catches environment drift — expired certificates, deprecated runner images, revoked tokens.

5. **Treat the pipeline as code that gets code-reviewed.** Workflow YAML changes go through PR review. A bad pipeline change breaks every build for every developer.

6. **Test OTA updates on staging before production.** Every OTA update goes to staging first, gets verified on a real device, then promotes. Skipping this is how you ship a white screen to 100,000 users.

7. **Measure build time monthly and set a budget.** If cached build takes 8 minutes today, alert at 12 minutes. Build time creeps up slowly without a budget.

8. **Keep signing credentials in exactly one place.** Not match repo plus GitHub Secrets plus a shared vault. One source of truth. Duplication is how credentials desync at 11pm before launch.

<!--
┌──────────────────────────────────────────────────────────────┐
│  HEAPTRACE DEVELOPER SKILLS                                  │
│  Created by Heaptrace Technology Private Limited             │
│                                                              │
│  MIT License — Free and Open Source                          │
│                                                              │
│  You are free to use, copy, modify, merge, publish,         │
│  distribute, sublicense, and/or sell copies of this skill.   │
│  No restrictions. No attribution required.                   │
│                                                              │
│  heaptrace.com | github.com/heaptracetechnology              │
└──────────────────────────────────────────────────────────────┘
-->

---
> Source: [heaptracetechnology/heaptrace-skills](https://github.com/heaptracetechnology/heaptrace-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
