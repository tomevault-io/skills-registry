---
name: metrickit-diagnostics
description: Native crash reporting, hang diagnostics, and performance metrics using Apple's MetricKit framework without third-party dependencies. Use this skill when implementing MXMetricManagerSubscriber, creating custom signpost categories, integrating OSLog with MetricKit, or processing diagnostic and metric payloads. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# MetricKit Diagnostics for iOS

## Overview

Implement native crash reporting, hang diagnostics, and performance metrics using Apple's MetricKit framework. This skill covers MXMetricManagerSubscriber protocol implementation, custom signpost categories for measuring video encoding and P2P streaming timing, OSLog integration, and processing diagnostic/metric payloads - all without third-party dependencies.

## Why MetricKit?

| Feature | MetricKit | Third-Party (Crashlytics, etc.) |
|---------|-----------|--------------------------------|
| Privacy | Apple-managed, no user ID | Requires user consent |
| Dependencies | None (built-in) | SDK integration |
| Data Access | Full JSON payloads | Dashboard-only |
| Cost | Free | Often paid at scale |
| Crash Symbolication | Automatic via App Store | Manual DSYM upload |
| Custom Signposts | Native integration | Limited |

## Minimum Requirements

- iOS 13+ for MXMetricPayload
- iOS 14+ for MXDiagnosticPayload (crash/hang reports)
- iOS 15+ for immediate diagnostic delivery (vs daily batches)

## Core Architecture

### DiagnosticsManager Singleton

```swift
import Foundation
import MetricKit
import OSLog

@MainActor
final class DiagnosticsManager: NSObject, ObservableObject {
    
    // MARK: - Singleton
    
    static let shared = DiagnosticsManager()
    
    // MARK: - Published State
    
    @Published private(set) var diagnosticPayloadsReceived: Int = 0
    @Published private(set) var metricPayloadsReceived: Int = 0
    @Published private(set) var lastPayloadTime: Date?
    
    // MARK: - Custom Signpost Log Handles
    
    private static let encodingLogHandle = MXMetricManager.makeLogHandle(category: "VideoEncoding")
    private static let p2pLogHandle = MXMetricManager.makeLogHandle(category: "P2PStreaming")
    private static let cameraLogHandle = MXMetricManager.makeLogHandle(category: "CameraOperations")
    private static let qrLogHandle = MXMetricManager.makeLogHandle(category: "QRCode")
    
    // MARK: - Initialization
    
    private override init() {
        super.init()
    }
    
    // MARK: - Public API
    
    func start() {
        AppLog.app.info("DiagnosticsManager: Starting MetricKit subscriber")
        MXMetricManager.shared.add(self)
    }
    
    func stop() {
        MXMetricManager.shared.remove(self)
    }
}
```

### MXMetricManagerSubscriber Conformance

```swift
extension DiagnosticsManager: MXMetricManagerSubscriber {
    
    /// Called when MetricKit delivers daily performance metrics
    nonisolated func didReceive(_ payloads: [MXMetricPayload]) {
        Task { @MainActor in
            AppLog.app.info("Received \(payloads.count) metric payload(s)")
            
            metricPayloadsReceived += payloads.count
            lastPayloadTime = Date()
            
            for payload in payloads {
                processMetricPayload(payload)
            }
        }
    }
    
    /// Called when MetricKit delivers diagnostic reports
    /// iOS 15+: Delivered immediately after crash/hang
    /// iOS 14: Delivered daily with metrics
    nonisolated func didReceive(_ payloads: [MXDiagnosticPayload]) {
        Task { @MainActor in
            AppLog.app.info("Received \(payloads.count) diagnostic payload(s)")
            
            diagnosticPayloadsReceived += payloads.count
            lastPayloadTime = Date()
            
            for payload in payloads {
                processDiagnosticPayload(payload)
            }
        }
    }
}
```

### nonisolated Requirement

The `MXMetricManagerSubscriber` methods are called on an arbitrary queue, so they must be `nonisolated`. Use `Task { @MainActor in }` to hop back to the main actor for UI updates.

## Custom Signposts

### Creating Log Handles

```swift
// Static log handles for each measurement category
private static let encodingLogHandle = MXMetricManager.makeLogHandle(category: "VideoEncoding")
private static let p2pLogHandle = MXMetricManager.makeLogHandle(category: "P2PStreaming")
private static let cameraLogHandle = MXMetricManager.makeLogHandle(category: "CameraOperations")
```

### Signpost Types

| Type | Usage | Example |
|------|-------|---------|
| `.begin` | Start measuring an interval | Frame encoding starts |
| `.end` | End measuring an interval | Frame encoding completes |
| `.event` | Record a point-in-time event | Frame dropped |

### Video Encoding Signposts

```swift
// MARK: - Video Encoding Signposts

func beginVideoEncode() {
    mxSignpost(.begin, log: Self.encodingLogHandle, name: "FrameEncode")
}

func endVideoEncode() {
    mxSignpost(.end, log: Self.encodingLogHandle, name: "FrameEncode")
}

func recordVideoEncodeEvent(frameSize: Int, isKeyframe: Bool) {
    mxSignpost(.event, log: Self.encodingLogHandle, name: "FrameEncoded")
    AppLog.encoding.debug("Frame: \(frameSize) bytes, keyframe: \(isKeyframe)")
}

func recordEncoderSessionCreated(width: Int, height: Int, bitrate: Int) {
    mxSignpost(.event, log: Self.encodingLogHandle, name: "SessionCreated")
    AppLog.encoding.info("Encoder: \(width)x\(height) @ \(bitrate/1000)kbps")
}
```

### P2P Streaming Signposts

```swift
// MARK: - P2P Streaming Signposts

func beginP2PConnection() {
    mxSignpost(.begin, log: Self.p2pLogHandle, name: "ConnectionEstablish")
}

func endP2PConnection(success: Bool) {
    mxSignpost(.end, log: Self.p2pLogHandle, name: "ConnectionEstablish")
    if success {
        AppLog.p2p.info("P2P connection established")
    } else {
        AppLog.p2p.warning("P2P connection failed")
    }
}

func recordFrameDrop(count: Int, sequenceNumber: UInt32) {
    mxSignpost(.event, log: Self.p2pLogHandle, name: "FrameDropped")
    AppLog.p2p.warning("Frame drop: \(count) frames at seq \(sequenceNumber)")
}

func recordP2PDisconnect(peerName: String, reason: String) {
    mxSignpost(.event, log: Self.p2pLogHandle, name: "Disconnected")
    AppLog.p2p.info("Disconnected from '\(peerName)': \(reason)")
}
```

### Camera Signposts

```swift
// MARK: - Camera Signposts

func beginCameraStart() {
    mxSignpost(.begin, log: Self.cameraLogHandle, name: "CameraStart")
}

func endCameraStart(success: Bool) {
    mxSignpost(.end, log: Self.cameraLogHandle, name: "CameraStart")
}

func recordCameraError(_ error: Error) {
    mxSignpost(.event, log: Self.cameraLogHandle, name: "CameraError")
    AppLog.camera.error("Camera error: \(error.localizedDescription)")
}
```

### QR Code Signposts

```swift
// MARK: - QR Code Signposts

func beginQRGeneration() {
    mxSignpost(.begin, log: Self.qrLogHandle, name: "QRGenerate")
}

func endQRGeneration() {
    mxSignpost(.end, log: Self.qrLogHandle, name: "QRGenerate")
}

func recordQRScan(success: Bool) {
    mxSignpost(.event, log: Self.qrLogHandle, name: "QRScanned")
}
```

## OSLog Integration

### Unified Logging Categories

```swift
import OSLog

enum AppLog {
    private static let subsystem = Bundle.main.bundleIdentifier ?? "com.example.app"
    
    // Logger categories matching MetricKit signpost categories
    static let app = Logger(subsystem: subsystem, category: "app")
    static let camera = Logger(subsystem: subsystem, category: "camera")
    static let encoding = Logger(subsystem: subsystem, category: "encoding")
    static let decoding = Logger(subsystem: subsystem, category: "decoding")
    static let p2p = Logger(subsystem: subsystem, category: "p2p")
    static let server = Logger(subsystem: subsystem, category: "server")
    static let network = Logger(subsystem: subsystem, category: "network")
    static let trust = Logger(subsystem: subsystem, category: "trust")
    static let qr = Logger(subsystem: subsystem, category: "qr")
    static let orientation = Logger(subsystem: subsystem, category: "orientation")
    static let performance = Logger(subsystem: subsystem, category: "performance")
}
```

### Category Alignment

Align OSLog categories with MetricKit signpost categories for consistent tracing:

| OSLog Category | MetricKit Category | Purpose |
|----------------|-------------------|---------|
| `encoding` | `VideoEncoding` | H.264 frame encoding |
| `p2p` | `P2PStreaming` | MultipeerConnectivity |
| `camera` | `CameraOperations` | AVFoundation capture |
| `qr` | `QRCode` | QR generation/scanning |

### Privacy-Aware Logging

```swift
// Public data (visible in Console.app)
AppLog.encoding.info("Frame size: \(frameSize, privacy: .public) bytes")

// Private data (redacted unless device is connected to Xcode)
AppLog.trust.info("Certificate ID: \(certId, privacy: .private)")

// Auto privacy (strings are private by default)
AppLog.p2p.info("Peer: \(peerName)")  // Redacted by default
```

### Timing Helper

```swift
extension Logger {
    func timing(_ message: String, since startTime: CFAbsoluteTime) {
        let elapsed = (CFAbsoluteTimeGetCurrent() - startTime) * 1000
        self.info("\(message): \(String(format: "%.1f", elapsed), privacy: .public)ms")
    }
}

// Usage
let start = CFAbsoluteTimeGetCurrent()
// ... do work ...
AppLog.encoding.timing("Frame encoded", since: start)
// Output: "Frame encoded: 12.3ms"
```

## Processing Diagnostic Payloads

### Crash Diagnostics

```swift
private func processDiagnosticPayload(_ payload: MXDiagnosticPayload) {
    AppLog.app.info("Processing diagnostic payload...")
    
    // Crash diagnostics
    if let crashDiagnostics = payload.crashDiagnostics {
        for crash in crashDiagnostics {
            AppLog.app.fault("CRASH: \(crash.callStackTree.jsonRepresentation())")
            
            if let terminationReason = crash.terminationReason {
                AppLog.app.fault("Termination: \(terminationReason)")
            }
            if let exceptionType = crash.exceptionType {
                AppLog.app.fault("Exception: \(exceptionType)")
            }
            if let signal = crash.signal {
                AppLog.app.fault("Signal: \(signal)")
            }
            if let virtualMemoryRegionInfo = crash.virtualMemoryRegionInfo {
                AppLog.app.fault("VM Region: \(virtualMemoryRegionInfo)")
            }
        }
    }
    
    // Hang diagnostics (iOS 14+)
    if let hangDiagnostics = payload.hangDiagnostics {
        for hang in hangDiagnostics {
            AppLog.app.warning("HANG: duration=\(hang.hangDuration.description)")
            AppLog.app.warning("Stack: \(hang.callStackTree.jsonRepresentation())")
        }
    }
    
    // CPU exception diagnostics
    if let cpuExceptions = payload.cpuExceptionDiagnostics {
        for exception in cpuExceptions {
            AppLog.performance.warning("CPU EXCEPTION: \(exception.callStackTree.jsonRepresentation())")
        }
    }
    
    // Disk write exception diagnostics
    if let diskExceptions = payload.diskWriteExceptionDiagnostics {
        for exception in diskExceptions {
            AppLog.performance.warning("DISK WRITE: \(exception.callStackTree.jsonRepresentation())")
        }
    }
    
    // Store for later upload
    storeDiagnosticPayload(payload.jsonRepresentation())
}
```

### Metric Payloads

```swift
private func processMetricPayload(_ payload: MXMetricPayload) {
    AppLog.app.info("Processing metric payload...")
    
    // App exit metrics
    if let exitMetrics = payload.applicationExitMetrics {
        let foreground = exitMetrics.foregroundExitData
        AppLog.app.info("Foreground exits - Normal: \(foreground.cumulativeNormalAppExitCount)")
        AppLog.app.info("Foreground exits - Abnormal: \(foreground.cumulativeAbnormalExitCount)")
        AppLog.app.info("Foreground exits - Memory: \(foreground.cumulativeMemoryResourceLimitExitCount)")
        AppLog.app.info("Foreground exits - Bad access: \(foreground.cumulativeBadAccessExitCount)")
        
        let background = exitMetrics.backgroundExitData
        AppLog.app.info("Background exits - Memory pressure: \(background.cumulativeMemoryPressureExitCount)")
        AppLog.app.info("Background exits - Suspended: \(background.cumulativeSuspendedWithLockedFileExitCount)")
    }
    
    // CPU metrics
    if let cpuMetrics = payload.cpuMetrics {
        AppLog.performance.info("CPU time: \(cpuMetrics.cumulativeCPUTime.description)")
    }
    
    // GPU metrics
    if let gpuMetrics = payload.gpuMetrics {
        AppLog.performance.info("GPU time: \(gpuMetrics.cumulativeGPUTime.description)")
    }
    
    // Memory metrics
    if let memoryMetrics = payload.memoryMetrics {
        AppLog.performance.info("Peak memory: \(memoryMetrics.peakMemoryUsage.description)")
    }
    
    // Animation metrics
    if let animationMetrics = payload.animationMetrics {
        AppLog.performance.info("Scroll hitch: \(animationMetrics.scrollHitchTimeRatio.description)")
    }
    
    // Store for later upload
    storeMetricPayload(payload.jsonRepresentation())
}
```

## Payload Storage

### Local Storage for Later Upload

```swift
private func storePayload(_ jsonData: Data, type: String) {
    guard let documentsPath = FileManager.default.urls(
        for: .documentDirectory, 
        in: .userDomainMask
    ).first else {
        AppLog.app.error("Failed to get documents directory")
        return
    }
    
    let diagnosticsDir = documentsPath.appendingPathComponent("MetricKit", isDirectory: true)
    
    // Create directory if needed
    do {
        try FileManager.default.createDirectory(
            at: diagnosticsDir, 
            withIntermediateDirectories: true
        )
    } catch {
        AppLog.app.error("Failed to create directory: \(error.localizedDescription)")
        return
    }
    
    // Generate timestamped filename
    let formatter = ISO8601DateFormatter()
    formatter.formatOptions = [.withInternetDateTime, .withDashSeparatorInDate]
    let timestamp = formatter.string(from: Date())
    let filename = "\(type)_\(timestamp).json"
    
    let fileURL = diagnosticsDir.appendingPathComponent(filename)
    
    do {
        try jsonData.write(to: fileURL)
        AppLog.app.info("Stored \(type) payload: \(filename)")
    } catch {
        AppLog.app.error("Failed to store payload: \(error.localizedDescription)")
    }
}

private func storeDiagnosticPayload(_ jsonData: Data) {
    storePayload(jsonData, type: "diagnostic")
}

private func storeMetricPayload(_ jsonData: Data) {
    storePayload(jsonData, type: "metric")
}
```

### Retrieving Stored Payloads

```swift
func getStoredPayloads() -> [(type: String, date: Date, data: Data)] {
    guard let documentsPath = FileManager.default.urls(
        for: .documentDirectory, 
        in: .userDomainMask
    ).first else {
        return []
    }
    
    let diagnosticsDir = documentsPath.appendingPathComponent("MetricKit")
    
    guard let files = try? FileManager.default.contentsOfDirectory(
        at: diagnosticsDir,
        includingPropertiesForKeys: [.creationDateKey]
    ) else {
        return []
    }
    
    return files.compactMap { url in
        guard let data = try? Data(contentsOf: url),
              let attributes = try? FileManager.default.attributesOfItem(atPath: url.path),
              let date = attributes[.creationDate] as? Date else {
            return nil
        }
        
        let type = url.lastPathComponent.hasPrefix("diagnostic") ? "diagnostic" : "metric"
        return (type, date, data)
    }
}
```

## Usage Examples

### App Initialization

```swift
@main
struct CarSeetApp: App {
    init() {
        // Start MetricKit subscriber early
        DiagnosticsManager.shared.start()
    }
    
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

### Instrumenting Video Encoding

```swift
class HardwareVideoEncoder {
    func encodeFrame(_ sampleBuffer: CMSampleBuffer) {
        DiagnosticsManager.shared.beginVideoEncode()
        
        // ... encoding work ...
        
        DiagnosticsManager.shared.endVideoEncode()
        DiagnosticsManager.shared.recordVideoEncodeEvent(
            frameSize: encodedData.count,
            isKeyframe: isKeyframe
        )
    }
}
```

### Instrumenting P2P Connection

```swift
class P2PStreamingManager {
    func connectToPeer(_ peer: MCPeerID) {
        DiagnosticsManager.shared.beginP2PConnection()
        
        browser.invitePeer(peer, to: session, withContext: nil, timeout: 30)
    }
    
    func session(_ session: MCSession, peer: MCPeerID, didChange state: MCSessionState) {
        switch state {
        case .connected:
            DiagnosticsManager.shared.endP2PConnection(success: true)
        case .notConnected:
            DiagnosticsManager.shared.endP2PConnection(success: false)
            DiagnosticsManager.shared.recordP2PDisconnect(
                peerName: peer.displayName,
                reason: "Connection lost"
            )
        case .connecting:
            break
        }
    }
}
```

### Instrumenting Camera Start

```swift
class CameraManager {
    func startCapture() async throws {
        DiagnosticsManager.shared.beginCameraStart()
        
        do {
            try await configureSession()
            session.startRunning()
            DiagnosticsManager.shared.endCameraStart(success: true)
        } catch {
            DiagnosticsManager.shared.endCameraStart(success: false)
            DiagnosticsManager.shared.recordCameraError(error)
            throw error
        }
    }
}
```

## Settings View Integration

### Displaying Diagnostic Stats

```swift
struct DiagnosticsSettingsView: View {
    @ObservedObject var diagnostics = DiagnosticsManager.shared
    
    var body: some View {
        Section("Diagnostics") {
            LabeledContent("Metric Payloads") {
                Text("\(diagnostics.metricPayloadsReceived)")
            }
            
            LabeledContent("Diagnostic Payloads") {
                Text("\(diagnostics.diagnosticPayloadsReceived)")
            }
            
            if let lastTime = diagnostics.lastPayloadTime {
                LabeledContent("Last Received") {
                    Text(lastTime, style: .relative)
                }
            }
        }
    }
}
```

## Testing MetricKit

### Simulating Payloads (Debug Only)

MetricKit payloads are delivered by the system. For testing:

1. **Xcode Organizer**: View real crash reports from TestFlight/App Store
2. **Instruments**: Use Signposts instrument to visualize custom signposts
3. **Console.app**: Filter by subsystem to see OSLog output

### Signpost Visualization in Instruments

1. Profile app with Instruments
2. Add "Signposts" instrument
3. Filter by category (e.g., "VideoEncoding")
4. View timing intervals and events

## Best Practices

### Signpost Naming

- Use descriptive names: `"FrameEncode"` not `"encode"`
- Keep names consistent across begin/end pairs
- Use PascalCase for signpost names

### Category Organization

- One category per major subsystem
- Align with OSLog categories
- Don't create too many categories (keep it manageable)

### Payload Processing

- Process payloads on MainActor for UI updates
- Store JSON locally for debugging
- Consider uploading to your backend for aggregation

### Memory Considerations

- MetricKit payloads can be large
- Process and discard, don't hold references
- Clean up old stored payloads periodically

## Reference Implementation

See the following files for the complete production implementation:

- `CarSeet/CarSeet/DiagnosticsManager.swift` - Full MetricKit integration
- `CarSeet/CarSeet/AppLogger.swift` - OSLog category definitions

## Payload JSON Structure

### MXDiagnosticPayload

```json
{
  "crashDiagnostics": [{
    "callStackTree": { ... },
    "terminationReason": "...",
    "exceptionType": 1,
    "signal": 11
  }],
  "hangDiagnostics": [{
    "hangDuration": "2.5 sec",
    "callStackTree": { ... }
  }],
  "cpuExceptionDiagnostics": [...],
  "diskWriteExceptionDiagnostics": [...]
}
```

### MXMetricPayload

```json
{
  "applicationExitMetrics": {
    "foregroundExitData": {
      "cumulativeNormalAppExitCount": 45,
      "cumulativeAbnormalExitCount": 2
    }
  },
  "cpuMetrics": {
    "cumulativeCPUTime": "1234.5 sec"
  },
  "memoryMetrics": {
    "peakMemoryUsage": "150 MB"
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
