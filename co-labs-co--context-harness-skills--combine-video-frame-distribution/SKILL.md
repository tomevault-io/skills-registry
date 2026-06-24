---
name: combine-video-frame-distribution
description: Thread-safe distribution of video frames (CVPixelBuffer) to multiple subscribers using Apple's Combine framework. Use this skill for real-time video streaming to multiple consumers, multi-subscriber broadcasting, cross-thread frame delivery, or replacing timer-based polling with push-based delivery. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# Skill: Combine Video Frame Distribution

Efficient, thread-safe distribution of video frames (CVPixelBuffer) from a camera capture pipeline to multiple concurrent subscribers using Apple's Combine framework.

## When to Use

- **Real-time video streaming**: Camera feed to multiple consumers (preview, encoder, analytics)
- **Multi-subscriber broadcasting**: Same frame data needed by different components
- **Cross-thread delivery**: Camera background queue to main thread UI updates
- **Event-driven architecture**: Replace timer-based polling with push-based frame delivery
- **SwiftUI integration**: Bridging camera callbacks to @Published properties

## Key Concepts

### PassthroughSubject vs CurrentValueSubject

| Aspect | PassthroughSubject | CurrentValueSubject |
|--------|-------------------|---------------------|
| Storage | None - immediate forwarding | Holds current value |
| New Subscribers | Receive nothing until next send | Immediately receive current value |
| Memory | Minimal overhead | Stores one value |
| Video Use Case | ✅ Correct choice | ❌ Wasteful for transient frames |

**Why PassthroughSubject for video**: Frames are transient (30-60 fps), new subscribers don't need stale frames.

### Thread Safety Model

PassthroughSubject is thread-safe for `send()`, but callbacks execute on the sender's thread:

```swift
// Subscriber receives on cameraQueue (the sending thread)
subject.sink { buffer in 
    // ⚠️ This runs on cameraQueue, NOT main thread
}

// Use receive(on:) for thread control
subject
    .receive(on: DispatchQueue.main)
    .sink { buffer in
        // ✅ This runs on main thread
    }
```

### Backpressure Strategy

Video frames are "hot" data—they arrive regardless of subscriber readiness:

| Strategy | Video Suitability |
|----------|------------------|
| No buffering (drop if busy) | ✅ Best for real-time |
| Buffer with limit | ⚠️ Adds latency |
| Unlimited buffer | ❌ Will crash |

## Implementation Guide

### Step 1: Define the Frame Publisher

```swift
import Combine
import AVFoundation

@MainActor
class CameraManager: NSObject, ObservableObject {
    // Thread-safe subject for frame broadcasting
    nonisolated(unsafe) private let frameSubject = PassthroughSubject<CVPixelBuffer, Never>()
    
    nonisolated var framePublisher: AnyPublisher<CVPixelBuffer, Never> {
        frameSubject.eraseToAnyPublisher()
    }
    
    private let cameraQueue = DispatchQueue(label: "camera.capture", qos: .userInteractive)
}
```

### Step 2: Publish Frames from Camera Callback

```swift
extension CameraManager: AVCaptureVideoDataOutputSampleBufferDelegate {
    nonisolated func captureOutput(
        _ output: AVCaptureOutput, 
        didOutput sampleBuffer: CMSampleBuffer, 
        from connection: AVCaptureConnection
    ) {
        guard let pixelBuffer = CMSampleBufferGetImageBuffer(sampleBuffer) else { return }
        
        // Broadcast to all subscribers (thread-safe)
        frameSubject.send(pixelBuffer)
    }
}
```

### Step 3: Subscribe for UI Updates (Main Thread)

```swift
class StreamingService: ObservableObject {
    private var cancellables = Set<AnyCancellable>()
    @Published var currentFrame: CVPixelBuffer?
    
    func setupPreviewSubscriber(camera: CameraManager) {
        camera.framePublisher
            .receive(on: DispatchQueue.main)  // Thread hop for UI safety
            .sink { [weak self] buffer in
                self?.currentFrame = buffer
            }
            .store(in: &cancellables)
    }
}
```

### Step 4: Subscribe for Encoding (Same Thread)

```swift
class VideoEncoder {
    private var frameSubscription: AnyCancellable?
    
    func startEncoding(from camera: CameraManager) {
        // No receive(on:) - stay on camera queue for performance
        frameSubscription = camera.framePublisher
            .sink { [weak self] buffer in
                self?.encode(buffer)  // Runs on camera queue
            }
    }
}
```

### Step 5: Throttle UI Updates

```swift
@MainActor
class P2PStreamingManager: ObservableObject {
    @Published var decodedPixelBuffer: CVPixelBuffer?
    
    // Backing store updated at full frame rate
    private(set) var latestDecodedFrame: CVPixelBuffer?
    
    // Throttle @Published to ~30Hz
    private var lastFramePublishTime: CFAbsoluteTime = 0
    private let framePublishInterval: CFAbsoluteTime = 1.0 / 30.0
    
    func handleDecodedFrame(_ buffer: CVPixelBuffer) {
        latestDecodedFrame = buffer
        
        let now = CFAbsoluteTimeGetCurrent()
        if now - lastFramePublishTime >= framePublishInterval {
            decodedPixelBuffer = buffer
            lastFramePublishTime = now
        }
    }
}
```

### Step 6: Multiple Subscriber Pattern

```swift
let framePublisher = camera.framePublisher

// UI Preview (main thread, throttled)
framePublisher
    .receive(on: DispatchQueue.main)
    .throttle(for: .milliseconds(33), scheduler: DispatchQueue.main, latest: true)
    .sink { updatePreview($0) }
    .store(in: &cancellables)

// H.264 Encoder (camera queue, full rate)
framePublisher
    .sink { encoder.encode($0) }
    .store(in: &cancellables)

// Statistics (sampled)
framePublisher
    .throttle(for: .seconds(1), scheduler: DispatchQueue.global(), latest: true)
    .sink { updateStatistics($0) }
    .store(in: &cancellables)
```

## Common Pitfalls

### 1. Using CurrentValueSubject for Video
```swift
// ❌ Wrong: Stores stale frames, wastes memory
let frameSubject = CurrentValueSubject<CVPixelBuffer?, Never>(nil)

// ✅ Correct: No storage for transient data
let frameSubject = PassthroughSubject<CVPixelBuffer, Never>()
```

### 2. Blocking the Camera Queue
```swift
// ❌ Wrong: Heavy work blocks frame capture
framePublisher.sink { buffer in
    let processed = expensiveOperation(buffer)
}

// ✅ Correct: Move work off camera queue
framePublisher
    .receive(on: DispatchQueue.global(qos: .userInitiated))
    .sink { buffer in
        let processed = expensiveOperation(buffer)
    }
```

### 3. Publishing @Published Too Frequently
```swift
// ❌ Wrong: iOS rate-limits at ~120Hz, causes warnings
@Published var frame: CVPixelBuffer?
framePublisher.sink { self.frame = $0 }  // 60fps = warning spam

// ✅ Correct: Throttle @Published updates
private(set) var latestFrame: CVPixelBuffer?  // Full rate
@Published var displayFrame: CVPixelBuffer?   // Throttled to 30Hz
```

### 4. Strong Reference Cycles
```swift
// ❌ Wrong: Strong self capture
framePublisher.sink { buffer in
    self.process(buffer)
}

// ✅ Correct: Weak self
framePublisher.sink { [weak self] buffer in
    self?.process(buffer)
}
```

### 5. Not Storing Subscriptions
```swift
// ❌ Wrong: Subscription immediately cancelled
func subscribe(to camera: CameraManager) {
    camera.framePublisher.sink { ... }  // Discarded!
}

// ✅ Correct: Store in Set
private var cancellables = Set<AnyCancellable>()

func subscribe(to camera: CameraManager) {
    camera.framePublisher
        .sink { ... }
        .store(in: &cancellables)
}
```

## Performance Tips

1. **Avoid unnecessary thread hops**: If encoder runs on camera queue, don't add `receive(on:)`
2. **Throttle UI updates**: SwiftUI doesn't need 60fps; 30Hz is sufficient
3. **Share expensive computations**: Use `.share()` if multiple subscribers need same transform
4. **Pre-allocate resources**: Create encoders/decoders before subscribing

## References

- [Apple Combine Framework](https://developer.apple.com/documentation/combine)
- [Using Combine - heckj.github.io](https://heckj.github.io/swiftui-notes/)
- [PassthroughSubject vs CurrentValueSubject](https://avanderlee.com/combine/passthroughsubject-currentvaluesubject-explained)

---
_Derived from CarSeet project - CameraManager.swift, P2PStreamingManager.swift_

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
