---
name: ios-cicd-testflight
description: Automate iOS builds, versioning, and TestFlight deployment using GitHub Actions with semantic-release. Use this skill when setting up automated TestFlight deployments, implementing semantic versioning with conventional commits, configuring Apple code signing in CI/CD, or automating changelog generation. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# Skill: iOS CI/CD with TestFlight

Automate iOS app builds, versioning, and TestFlight deployment using GitHub Actions with semantic-release for conventional commits-based version management.

## When to Use

- Setting up automated TestFlight deployments for iOS apps
- Implementing semantic versioning with conventional commits
- Configuring Apple code signing in CI/CD pipelines
- Automating version bumps and changelog generation
- Building release pipelines that trigger on Git tags

## Key Concepts

### Version Management for iOS
- **CFBundleShortVersionString** (Marketing version): User-visible version (1.2.3)
- **CFBundleVersion** (Build number): Must increment with each App Store Connect submission

### Apple Code Signing Components
1. **Distribution Certificate (.p12)**: Proves your identity to Apple
2. **Provisioning Profile (.mobileprovision)**: Links app, certificate, and distribution method
3. **App Store Connect API Key (.p8)**: Authenticates CLI uploads

### GitHub Actions Secrets Required
```
APPLE_TEAM_ID
DISTRIBUTION_CERTIFICATE_BASE64
DISTRIBUTION_CERTIFICATE_PASSWORD
PROVISIONING_PROFILE_BASE64
APP_STORE_CONNECT_API_KEY_ID
APP_STORE_CONNECT_API_ISSUER_ID
APP_STORE_CONNECT_API_KEY_BASE64
```

## Implementation Guide

### Step 1: Configure semantic-release

**package.json:**
```json
{
  "devDependencies": {
    "@semantic-release/changelog": "^6.0.3",
    "@semantic-release/commit-analyzer": "^13.0.0",
    "@semantic-release/exec": "^7.1.0",
    "@semantic-release/git": "^10.0.1",
    "@semantic-release/github": "^12.0.2",
    "@semantic-release/release-notes-generator": "^14.0.0",
    "semantic-release": "^25.0.2"
  }
}
```

**.releaserc.json:**
```json
{
  "branches": ["main"],
  "tagFormat": "v${version}",
  "plugins": [
    ["@semantic-release/commit-analyzer", {
      "preset": "conventionalcommits",
      "releaseRules": [
        { "type": "feat", "release": "minor" },
        { "type": "fix", "release": "patch" },
        { "type": "perf", "release": "patch" }
      ]
    }],
    ["@semantic-release/release-notes-generator", {
      "preset": "conventionalcommits"
    }],
    ["@semantic-release/changelog", {
      "changelogFile": "CHANGELOG.md"
    }],
    ["@semantic-release/exec", {
      "prepareCmd": "scripts/update-ios-version.sh ${nextRelease.version}"
    }],
    ["@semantic-release/git", {
      "assets": ["CHANGELOG.md", "YourApp/Info.plist"],
      "message": "chore(release): ${nextRelease.version} [skip ci]"
    }],
    ["@semantic-release/github"]
  ]
}
```

**scripts/update-ios-version.sh:**
```bash
#!/bin/bash
VERSION=$1
BUILD_NUMBER=$(date +%Y%m%d%H%M)

/usr/libexec/PlistBuddy -c "Set :CFBundleShortVersionString $VERSION" "YourApp/Info.plist"
/usr/libexec/PlistBuddy -c "Set :CFBundleVersion $BUILD_NUMBER" "YourApp/Info.plist"
```

### Step 2: TestFlight Workflow

**.github/workflows/testflight.yml:**
```yaml
name: TestFlight
version: 0.1.0
author: cmtzco

on:
  push:
    tags: ['v*.*.*']
  workflow_dispatch:

jobs:
  build-and-upload:
    runs-on: macos-15
    
    steps:
      - uses: actions/checkout@v4

      - name: Set version
        id: version
        run: |
          if [[ "${{ github.ref }}" == refs/tags/v* ]]; then
            VERSION="${GITHUB_REF#refs/tags/v}"
          else
            VERSION=$(/usr/libexec/PlistBuddy -c "Print :CFBundleShortVersionString" "YourApp/Info.plist")
          fi
          BUILD_NUMBER=$(date +%Y%m%d%H%M)
          echo "version=$VERSION" >> $GITHUB_OUTPUT
          echo "build_number=$BUILD_NUMBER" >> $GITHUB_OUTPUT

      - name: Install certificate and profile
        env:
          DISTRIBUTION_CERTIFICATE_BASE64: ${{ secrets.DISTRIBUTION_CERTIFICATE_BASE64 }}
          DISTRIBUTION_CERTIFICATE_PASSWORD: ${{ secrets.DISTRIBUTION_CERTIFICATE_PASSWORD }}
          PROVISIONING_PROFILE_BASE64: ${{ secrets.PROVISIONING_PROFILE_BASE64 }}
          KEYCHAIN_PASSWORD: ${{ github.run_id }}
        run: |
          CERTIFICATE_PATH=$RUNNER_TEMP/cert.p12
          PROFILE_PATH=$RUNNER_TEMP/profile.mobileprovision
          KEYCHAIN_PATH=$RUNNER_TEMP/app-signing.keychain-db
          
          echo -n "$DISTRIBUTION_CERTIFICATE_BASE64" | base64 --decode -o "$CERTIFICATE_PATH"
          echo -n "$PROVISIONING_PROFILE_BASE64" | base64 --decode -o "$PROFILE_PATH"
          
          security create-keychain -p "$KEYCHAIN_PASSWORD" "$KEYCHAIN_PATH"
          security set-keychain-settings -lut 21600 "$KEYCHAIN_PATH"
          security unlock-keychain -p "$KEYCHAIN_PASSWORD" "$KEYCHAIN_PATH"
          
          security import "$CERTIFICATE_PATH" -P "$DISTRIBUTION_CERTIFICATE_PASSWORD" -A -t cert -f pkcs12 -k "$KEYCHAIN_PATH"
          security set-key-partition-list -S apple-tool:,apple: -k "$KEYCHAIN_PASSWORD" "$KEYCHAIN_PATH"
          security list-keychain -d user -s "$KEYCHAIN_PATH"
          
          mkdir -p ~/Library/MobileDevice/Provisioning\ Profiles
          cp "$PROFILE_PATH" ~/Library/MobileDevice/Provisioning\ Profiles/

      - name: Build archive
        run: |
          xcodebuild archive \
            -project "YourApp.xcodeproj" \
            -scheme "YourApp" \
            -destination "generic/platform=iOS" \
            -archivePath "$RUNNER_TEMP/App.xcarchive" \
            CODE_SIGN_STYLE=Manual \
            DEVELOPMENT_TEAM="${{ secrets.APPLE_TEAM_ID }}"

      - name: Export IPA
        run: |
          cat > "$RUNNER_TEMP/ExportOptions.plist" << EOF
          <?xml version="1.0" encoding="UTF-8"?>
          <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
          <plist version="1.0">
          <dict>
              <key>method</key>
              <string>app-store-connect</string>
              <key>teamID</key>
              <string>${{ secrets.APPLE_TEAM_ID }}</string>
          </dict>
          </plist>
          EOF
          
          xcodebuild -exportArchive \
            -archivePath "$RUNNER_TEMP/App.xcarchive" \
            -exportPath "$RUNNER_TEMP/export" \
            -exportOptionsPlist "$RUNNER_TEMP/ExportOptions.plist"

      - name: Upload to TestFlight
        env:
          APP_STORE_CONNECT_API_KEY_ID: ${{ secrets.APP_STORE_CONNECT_API_KEY_ID }}
          APP_STORE_CONNECT_API_ISSUER_ID: ${{ secrets.APP_STORE_CONNECT_API_ISSUER_ID }}
          APP_STORE_CONNECT_API_KEY_BASE64: ${{ secrets.APP_STORE_CONNECT_API_KEY_BASE64 }}
        run: |
          API_KEY_PATH=$RUNNER_TEMP/AuthKey.p8
          echo -n "$APP_STORE_CONNECT_API_KEY_BASE64" | base64 --decode -o "$API_KEY_PATH"
          
          mkdir -p ~/.private_keys
          cp "$API_KEY_PATH" ~/.private_keys/AuthKey_${APP_STORE_CONNECT_API_KEY_ID}.p8
          
          IPA_PATH=$(find "$RUNNER_TEMP/export" -name "*.ipa" | head -1)
          
          xcrun altool --upload-app \
            --type ios \
            --file "$IPA_PATH" \
            --apiKey "$APP_STORE_CONNECT_API_KEY_ID" \
            --apiIssuer "$APP_STORE_CONNECT_API_ISSUER_ID"

      - name: Clean up keychain
        if: always()
        run: security delete-keychain $RUNNER_TEMP/app-signing.keychain-db 2>/dev/null || true
```

## Common Pitfalls

### Code Signing Errors
- **"No signing certificate"**: Ensure certificate exported with private key as .p12
- **"Provisioning profile doesn't match"**: Bundle ID must match exactly
- **"Team ID mismatch"**: Use 10-character Team ID, not team name

### Build Number Conflicts
- **"Build already processed"**: Build numbers must strictly increase
- **Fix**: Timestamp format (YYYYMMDDHHMM) prevents duplicates

### Secrets Encoding
```bash
# Correct encoding on macOS
base64 -i certificate.p12 -o cert.base64

# Use echo -n when decoding to avoid trailing newlines
echo -n "$SECRET" | base64 --decode -o file
```

### semantic-release Issues
- **"ENOGHTOKEN"**: Ensure GITHUB_TOKEN has write permissions
- **"No release"**: Commits must follow conventional format (`feat:`, `fix:`)
- **"PlistBuddy not found"**: Must run on macOS runner

### Runner Selection
- **macos-15**: Recommended (latest Xcode, Apple Silicon)
- **macos-latest**: Will migrate to macos-15 in August 2025

## References

- [GitHub: Installing Apple Certificate on macOS Runners](https://docs.github.com/en/actions/deployment/deploying-xcode-applications/installing-an-apple-certificate-on-macos-runners-for-xcode-development)
- [GitHub Actions macOS Runners](https://github.com/actions/runner-images/blob/main/images/macos/macos-15-Readme.md)
- [semantic-release Documentation](https://semantic-release.gitbook.io/semantic-release)
- [Apple: Creating API Keys](https://developer.apple.com/documentation/appstoreconnectapi/creating-api-keys-for-app-store-connect-api)

---
_Derived from CarSeet project - .github/workflows/testflight.yml, .releaserc.json_

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
