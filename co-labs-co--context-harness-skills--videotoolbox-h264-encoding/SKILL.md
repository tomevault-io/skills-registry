---
name: videotoolbox-h264-encoding
description: Hardware-accelerated H.264 video encoding using Apple's VideoToolbox framework for iOS/macOS. Use this skill when building real-time video streaming apps, implementing WebRTC solutions, creating live broadcasting with low-latency requirements, or encoding camera frames for network transmission. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# Skill: VideoToolbox H.264 Encoding

Hardware-accelerated H.264 video encoding using Apple's VideoToolbox framework for iOS/macOS real-time streaming applications. Covers VTCompressionSession lifecycle, safe C callback handling, session pre-warming for zero-latency startup, and dynamic bitrate optimization.

## When to Use

- Building real-time video streaming applications (baby monitors, security cameras)
- Implementing WebRTC or custom video calling solutions
- Creating live broadcasting apps with low-latency requirements
- Encoding camera frames for network transmission
- Orientation-adaptive video encoding with instant resolution switching

## Key Concepts

### VTCompressionSession
The core VideoToolbox object that performs hardware-accelerated video encoding. It:
- Takes CVPixelBuffer input (from camera or other sources)
- Outputs CMSampleBuffer with compressed H.264 NAL units
- Runs on dedicated hardware encoder (power-efficient)
- Requires proper lifecycle management (create → configure → prepare → encode → invalidate)

### Annex-B vs AVCC Format
- **AVCC** (VideoToolbox output): NAL units prefixed with 4-byte length
- **Annex-B** (network/file standard): NAL units prefixed with start code (0x00 0x00 0x00 0x01)
- Must convert AVCC → Annex-B for most streaming protocols

### Parameter Sets (SPS/PPS)
- **SPS** (Sequence Parameter Set): Video dimensions, profile, level
- **PPS** (Picture Parameter Set): Encoding settings
- Must be sent before or with each keyframe for decoder initialization
- Extract from format description on keyframes

### Keyframes (IDR Frames)
- Self-contained frames that don't reference other frames
- Required for decoder initialization and seeking
- Force periodically (every 2 seconds recommended for streaming)
- Larger than P-frames; plan for burst bandwidth

## Implementation Guide

### Step 1: Create the Encoder Class Structure

```swift
import VideoToolbox
import CoreMedia

class HardwareVideoEncoder {
    
    // MARK: - Weak Reference Wrapper for Safe Callbacks
    
    /// Prevents crashes when callbacks arrive after encoder deallocation
    final class CallbackContext {
        weak var encoder: HardwareVideoEncoder?
        
        init(encoder: HardwareVideoEncoder) {
            self.encoder = encoder
        }
    }
    
    // MARK: - Properties
    
    private var compressionSession: VTCompressionSession?
    private var callbackContext: CallbackContext?
    private var isReady = false
    private var isStopped = false  // Idempotency guard
    
    private var currentWidth: Int32 = 0
    private var currentHeight: Int32 = 0
    private let fps: Int32
    private var bitrate: Int32
    private var frameCount: Int64 = 0
    
    // Stored parameter sets
    private var spsData: Data?
    private var ppsData: Data?
    
    /// Callback for encoded frames
    var onEncodedFrame: ((EncodedFrame) -> Void)?
    
    init(fps: Int32 = 30, bitrate: Int32 = 1_500_000) {
        self.fps = fps
        self.bitrate = bitrate
    }
    
    deinit {
        stop()
    }
}
```

### Step 2: Session Creation with Hardware Encoder

```swift
private func createSession(width: Int32, height: Int32) throws {
    // Clean up existing session
    if let session = compressionSession {
        VTCompressionSessionCompleteFrames(session, untilPresentationTimeStamp: .invalid)
        VTCompressionSessionInvalidate(session)
        compressionSession = nil
    }
    
    var session: VTCompressionSession?
    
    // Create callback context with weak reference
    if callbackContext == nil {
        callbackContext = CallbackContext(encoder: self)
    } else {
        callbackContext?.encoder = self
    }
    
    // Create session - nil encoderSpecification prefers hardware encoder
    let status = VTCompressionSessionCreate(
        allocator: kCFAllocatorDefault,
        width: width,
        height: height,
        codecType: kCMVideoCodecType_H264,
        encoderSpecification: nil,  // nil = prefer hardware encoder
        imageBufferAttributes: nil,
        compressedDataAllocator: nil,
        outputCallback: encoderOutputCallback,
        refcon: Unmanaged.passRetained(callbackContext!).toOpaque(),
        compressionSessionOut: &session
    )
    
    guard status == noErr, let session = session else {
        throw EncoderError.failedToCreateSession
    }
    
    try configureSession(session)
    
    // Pre-warm: allocates hardware resources
    let prepareStatus = VTCompressionSessionPrepareToEncodeFrames(session)
    guard prepareStatus == noErr else {
        throw EncoderError.failedToConfigure
    }
    
    self.compressionSession = session
    self.currentWidth = width
    self.currentHeight = height
    self.isReady = true
    self.isStopped = false
    self.frameCount = 0
}
```

### Step 3: Session Configuration for Low-Latency Streaming

```swift
private func configureSession(_ session: VTCompressionSession) throws {
    var status: OSStatus
    
    // Real-time encoding (prioritize latency over quality)
    status = VTSessionSetProperty(
        session, 
        key: kVTCompressionPropertyKey_RealTime, 
        value: kCFBooleanTrue
    )
    guard status == noErr else { throw EncoderError.failedToConfigure }
    
    // Baseline profile for maximum compatibility and lowest latency
    status = VTSessionSetProperty(
        session, 
        key: kVTCompressionPropertyKey_ProfileLevel,
        value: kVTProfileLevel_H264_Baseline_AutoLevel
    )
    guard status == noErr else { throw EncoderError.failedToConfigure }
    
    // Average bitrate
    status = VTSessionSetProperty(
        session, 
        key: kVTCompressionPropertyKey_AverageBitRate,
        value: bitrate as CFNumber
    )
    guard status == noErr else { throw EncoderError.failedToConfigure }
    
    // Keyframe interval (max 2 seconds for streaming)
    status = VTSessionSetProperty(
        session, 
        key: kVTCompressionPropertyKey_MaxKeyFrameInterval,
        value: (fps * 2) as CFNumber
    )
    guard status == noErr else { throw EncoderError.failedToConfigure }
    
    // Disable B-frames for lowest latency
    status = VTSessionSetProperty(
        session, 
        key: kVTCompressionPropertyKey_AllowFrameReordering,
        value: kCFBooleanFalse
    )
    guard status == noErr else { throw EncoderError.failedToConfigure }
}
```

### Step 4: Safe C Callback Implementation

```swift
// Global C callback function
private func encoderOutputCallback(
    outputCallbackRefCon: UnsafeMutableRawPointer?,
    sourceFrameRefCon: UnsafeMutableRawPointer?,
    status: OSStatus,
    infoFlags: VTEncodeInfoFlags,
    sampleBuffer: CMSampleBuffer?
) {
    guard status == noErr, 
          let sampleBuffer = sampleBuffer,
          let refCon = outputCallbackRefCon else { return }
    
    // SAFE PATTERN: Extract CallbackContext and check weak reference
    let context = Unmanaged<HardwareVideoEncoder.CallbackContext>
        .fromOpaque(refCon)
        .takeUnretainedValue()
    
    // Check if encoder still exists
    guard let encoder = context.encoder else {
        // Encoder was deallocated - safely ignore callback
        return
    }
    
    encoder.handleEncodedFrame(sampleBuffer)
}
```

### Step 5: Session Pre-Warming for Instant Switching

```swift
// Pre-warmed session for orientation changes
private var prewarmedSession: VTCompressionSession?
private var prewarmedDimensions: (width: Int32, height: Int32)?

/// Pre-warm session for anticipated resolution change
func prewarmSession(for width: Int32, height: Int32) {
    // Create and configure session...
    
    // KEY: Pre-warm allocates hardware resources (~200ms saved)
    let prepareStatus = VTCompressionSessionPrepareToEncodeFrames(newSession)
    guard prepareStatus == noErr else { return }
    
    self.prewarmedSession = newSession
    self.prewarmedDimensions = (width, height)
}

/// Switch to pre-warmed session (near-instant)
@discardableResult
func switchToPrewarmedSession() -> Bool {
    guard let prewarmed = prewarmedSession else { return false }
    
    // Atomic swap
    let oldSession = compressionSession
    compressionSession = prewarmed
    prewarmedSession = nil
    
    // Invalidate old after swap
    if let old = oldSession {
        VTCompressionSessionInvalidate(old)
    }
    
    return true
}
```

### Step 6: Dynamic Bitrate Calculation

```swift
/// Calculate recommended bitrate based on resolution and FPS
static func recommendedBitrate(width: Int32, height: Int32, fps: Int32) -> Int32 {
    let pixels = width * height
    
    // Base bitrates at 30fps (industry standard)
    let baseBitrate: Int32
    switch pixels {
    case ..<500_000:           // 480p
        baseBitrate = 1_500_000     // 1.5 Mbps
    case 500_000..<1_000_000:  // 720p
        baseBitrate = 3_000_000     // 3 Mbps
    case 1_000_000..<4_000_000: // 1080p
        baseBitrate = 6_000_000     // 6 Mbps
    default:                    // 4K
        baseBitrate = 20_000_000    // 20 Mbps
    }
    
    // Scale linearly with FPS
    let fpsMultiplier = Double(fps) / 30.0
    return Int32(Double(baseBitrate) * fpsMultiplier)
}
```

## Common Pitfalls

### 1. C Callback Crashes After Deallocation
**Problem**: VideoToolbox callbacks arrive after encoder is deallocated → EXC_BAD_ACCESS
**Solution**: Use CallbackContext pattern with weak reference. Check `context.encoder != nil` before accessing.

### 2. Double-Invalidate Crash
**Problem**: Calling `VTCompressionSessionInvalidate` on already-invalidated session crashes
**Solution**: Use idempotency guard (`isStopped` flag) in `stop()` method.

### 3. First-Frame Latency (~200ms)
**Problem**: First encoded frame takes significantly longer due to hardware allocation
**Solution**: Call `VTCompressionSessionPrepareToEncodeFrames()` after configuration to pre-warm.

### 4. Resolution Change Stutter
**Problem**: Orientation changes require new session → visible delay
**Solution**: Pre-warm session for opposite orientation. Switch atomically when needed.

### 5. AVCC Format Incompatibility
**Problem**: VideoToolbox outputs AVCC format; most protocols expect Annex-B
**Solution**: Convert 4-byte length prefix to start codes (0x00 0x00 0x00 0x01).

### 6. Missing SPS/PPS on Viewer Join
**Problem**: Viewer joining mid-stream can't decode until next keyframe
**Solution**: Cache SPS/PPS and send with every keyframe. Consider IDR on viewer connect.

## References

- [WWDC2021: Explore low-latency video encoding with VideoToolbox](https://developer.apple.com/videos/play/wwdc2021/10158/)
- [Apple VideoToolbox Documentation](https://developer.apple.com/documentation/videotoolbox)
- [VTCompressionSession API Reference](https://developer.apple.com/documentation/videotoolbox/vtcompressionsession)

---
_Derived from CarSeet project - HardwareVideoEncoder.swift_

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
