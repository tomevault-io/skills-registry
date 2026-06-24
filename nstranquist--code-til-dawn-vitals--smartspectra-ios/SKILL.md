---
name: smartspectra-ios
description: Integrate the SmartSpectra vitals-from-camera SDK into an iOS/Swift app via its public API (Swift Package Manager package, NSCameraUsageDescription, SmartSpectraSDK.shared config, async start/stop, observing pulse/breathing/HRV/expression and the camera imageOutput), AND build/run this repo's swift sample apps. Use for any Swift/iOS/SwiftUI SmartSpectra work. Use when this capability is needed.
metadata:
  author: nstranquist
---

# SmartSpectra on iOS (Swift)

Entry point: **`SmartSpectraSDK.shared`** (singleton). **iOS 17+**, SwiftUI or UIKit.
**Runs only on a physical device — not the simulator** (needs the real camera). Canonical
source: `swift/docs/option-1-api-key.md` (API key) and `option-2-oauth.md` (OAuth). Result
data model: use the **`smartspectra-metrics`** skill.

## A. Add SmartSpectra to your own app

**1. Add the package** — Xcode → File → Add Package Dependencies → paste
`https://github.com/Presage-Security/SmartSpectra-Swift/`. For repeatable builds choose an
**Exact Version** tag (e.g. `3.1.0`); use `Branch: main` only to test the latest release.
Attach the `SmartSpectra` product to your app target.

**2. Camera permission** — add **`NSCameraUsageDescription`** ("Privacy - Camera Usage
Description") to the target's Info with a string like `This app needs camera access to measure
vitals.` (no key = immediate crash on start).

**3. Authenticate** — `sdk.config.apiKey = "YOUR_API_KEY"`. Get a key at
<https://physiology.presagetech.com/auth/register>. In API-key mode this log line is **normal,
not an error**: `PresageService-Info.plist not found. OAuth ... disabled. Using API key ...`.
Production → OAuth via `PresageService-Info.plist`, see `swift/docs/option-2-oauth.md`.

**4. Configure, start, observe** — the canonical shape:
```swift
import SwiftUI
import SmartSpectra
import AVFoundation

let sdk = SmartSpectraSDK.shared
sdk.config.apiKey = "YOUR_API_KEY"
sdk.config.cameraPosition = .front
sdk.config.imageOutputEnabled = true                        // exposes sdk.imageOutput (UIImage) for preview
sdk.config.requestedMetrics =
    SmartSpectraConfig.breathingMetrics +
    SmartSpectraConfig.cardioMetrics + [.expressions]

await sdk.start()          // async; stop is throwing: try? await sdk.stop()
```
`SmartSpectraSDK` is observable — in SwiftUI just read its published properties in `body`:
- `sdk.processingStatus` → `.idle/.starting/.running/.stopping/.error`
- `sdk.validationStatus?.code` → `.ok/.noFaceFound/.faceNotCentered/.tooDark/...`
- `sdk.imageOutput` → `UIImage?` camera preview (when `imageOutputEnabled`)
- `sdk.metrics` → `Metrics?`

Reading metrics (arrays, optional `.last`):
`sdk.metrics?.cardio.pulseRate.last?.value`, `...breathing.rate.last?.value`,
`...cardio.hrv.last?.rmssd`, `...cardio.arterialPressureTrace`, `...breathing.upperTrace` /
`lowerTrace`, `...face.expression.last?.scores` (pick max `confidence`). Sample types:
`MeasurementWithConfidence`, `SmartSpectra.Measurement`, `Hrv`, `ExpressionScore`. The SDK
adds an `appendProtoArray(contentsOf:)` helper for buffering trace arrays.

**Remember the streaming rule:** payloads are incremental — buffer trace samples, retain the
last rate value, drive a `.task(id:)` off the newest sample `timestamp`. (See
`smartspectra-metrics`.) Use `confidence` to color rate values (sample uses red <60 / yellow /
green ≥85).

The complete, paste-ready dashboard view is in **`templates/ContentView.swift`** — a
self-contained charted UI (camera preview, confidence-colored pulse/breathing/HRV/expression
cards, and live arterial-pressure + chest + abdomen waveforms via a built-in `WaveformView`).
It's the canonical file from `swift/docs/option-1-api-key.md`; drop it in as the whole
`ContentView.swift`, set the `apiKey`, and run on device.

## B. Build & run this repo's swift samples

Samples live in `swift/samples/`: `demo-app.xcodeproj` (SwiftUI, with
`demo-app-UITests`) and `uikit-sample.xcodeproj` (UIKit); the umbrella workspace is
`swift/SmartSpectra.xcworkspace`. Easiest path: open the workspace/project in Xcode, set a
development Team under Signing & Capabilities, select a **physical iPhone**, and Run. Headless
build from CLI:
```bash
xcodebuild -project swift/samples/demo-app.xcodeproj -scheme demo-app \
  -destination 'generic/platform=iOS' build
# list schemes/destinations:
xcodebuild -project swift/samples/demo-app.xcodeproj -list
```
Grant camera permission on first launch; allow a few seconds for camera tuning. Don't commit
a real API key.

## Going deeper

`swift/docs/api-reference.md` (full surface), `metrics.md` (accessors), `use-case-examples.md`,
`headless-mode.md`, `troubleshooting.md`, `migration-guide.md`. For interpreting results, use
the **`smartspectra-metrics`** skill.

---
> Source: [nstranquist/code-til-dawn-vitals](https://github.com/nstranquist/code-til-dawn-vitals) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
