---
name: swiftui-avfoundation-camera-bridge
description: Integrating AVCaptureVideoPreviewLayer into SwiftUI using UIViewRepresentable. Use this skill when building camera preview interfaces in SwiftUI, implementing real-time video capture views, adding pinch-to-zoom or gesture-based camera controls, or handling camera orientation properly. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# Skill: SwiftUI-AVFoundation Camera Bridge

Integrating AVCaptureVideoPreviewLayer into SwiftUI using UIViewRepresentable, with support for orientation handling, gesture recognition, and proper lifecycle management.

## When to Use

- Building camera preview interfaces in SwiftUI applications
- Integrating real-time video capture with SwiftUI views
- Implementing pinch-to-zoom or other gesture-based camera controls
- Creating camera apps that need proper orientation handling
- Bridging any AVFoundation preview layer into SwiftUI

## Key Concepts

### UIViewRepresentable Protocol
The bridge between UIKit and SwiftUI:
- `makeUIView(context:)` - Creates the UIKit view once
- `updateUIView(_:context:)` - Called when SwiftUI state changes

### Coordinator Pattern
Handles UIKit delegate patterns and gesture recognition:
- Created once via `makeCoordinator()`
- Persists across view updates
- Ideal for gesture recognizer targets

### iOS 17+ Orientation API
The modern `videoRotationAngle` property (CGFloat degrees) replaces deprecated `videoOrientation`:
- **0°** = Landscape Right
- **90°** = Portrait
- **180°** = Landscape Left
- **270°** = Portrait Upside Down

## Implementation Guide

### Step 1: Create the Custom UIView Subclass

```swift
import UIKit
import AVFoundation

class CameraPreviewUIView: UIView {
    var previewLayer: AVCaptureVideoPreviewLayer? {
        didSet {
            oldValue?.removeFromSuperlayer()
            if let layer = previewLayer {
                self.layer.addSublayer(layer)
                setNeedsLayout()
            }
        }
    }
    
    override func layoutSubviews() {
        super.layoutSubviews()
        previewLayer?.frame = bounds
        updatePreviewOrientation()
    }
    
    func updatePreviewOrientation() {
        guard let connection = previewLayer?.connection else { return }
        
        let interfaceOrientation = window?.windowScene?.interfaceOrientation ?? .portrait
        let angle = interfaceOrientation.videoRotationAngle
        
        if connection.isVideoRotationAngleSupported(angle) {
            connection.videoRotationAngle = angle
        }
    }
}

extension UIInterfaceOrientation {
    var videoRotationAngle: CGFloat {
        switch self {
        case .portrait: return 90
        case .portraitUpsideDown: return 270
        case .landscapeLeft: return 180
        case .landscapeRight: return 0
        default: return 90
        }
    }
}
```

### Step 2: Implement UIViewRepresentable

```swift
import SwiftUI
import AVFoundation

struct CameraPreviewView: UIViewRepresentable {
    let cameraManager: CameraManager
    var onZoomChange: ((CGFloat, CGFloat) -> Void)?
    
    func makeUIView(context: Context) -> CameraPreviewUIView {
        let view = CameraPreviewUIView()
        view.previewLayer = cameraManager.makePreviewLayer()
        
        // Add pinch gesture targeting the coordinator
        let pinchGesture = UIPinchGestureRecognizer(
            target: context.coordinator,
            action: #selector(Coordinator.handlePinch(_:))
        )
        view.addGestureRecognizer(pinchGesture)
        
        return view
    }
    
    func updateUIView(_ uiView: CameraPreviewUIView, context: Context) {
        uiView.updatePreviewOrientation()
        context.coordinator.onZoomChange = onZoomChange
    }
    
    func makeCoordinator() -> Coordinator {
        Coordinator(cameraManager: cameraManager, onZoomChange: onZoomChange)
    }
}
```

### Step 3: Implement the Coordinator

```swift
extension CameraPreviewView {
    class Coordinator: NSObject {
        let cameraManager: CameraManager
        private var baseZoomFactor: CGFloat = 1.0
        var onZoomChange: ((CGFloat, CGFloat) -> Void)?
        
        init(cameraManager: CameraManager, onZoomChange: ((CGFloat, CGFloat) -> Void)?) {
            self.cameraManager = cameraManager
            self.onZoomChange = onZoomChange
        }
        
        @MainActor @objc func handlePinch(_ gesture: UIPinchGestureRecognizer) {
            switch gesture.state {
            case .began:
                baseZoomFactor = cameraManager.currentZoomFactor
                
            case .changed:
                cameraManager.applyZoomDelta(
                    scale: gesture.scale,
                    baseZoom: baseZoomFactor
                )
                onZoomChange?(
                    cameraManager.currentZoomFactor,
                    cameraManager.maxZoomFactor
                )
                
            case .ended, .cancelled:
                cameraManager.finalizeZoom()
                
            default:
                break
            }
        }
    }
}
```

### Step 4: Usage in SwiftUI

```swift
struct CameraScreen: View {
    @StateObject private var cameraManager = CameraManager()
    @State private var currentZoom: CGFloat = 1.0
    
    var body: some View {
        ZStack {
            CameraPreviewView(
                cameraManager: cameraManager,
                onZoomChange: { current, max in
                    currentZoom = current
                }
            )
            .ignoresSafeArea()
            
            VStack {
                Spacer()
                Text(String(format: "%.1fx", currentZoom))
                    .padding()
                    .background(.ultraThinMaterial)
                    .cornerRadius(8)
            }
        }
        .onAppear { cameraManager.startSession() }
        .onDisappear { cameraManager.stopSession() }
    }
}
```

## Common Pitfalls

### 1. Layer Frame Not Updating
**Problem**: Preview layer doesn't resize with the view.
**Solution**: Update `previewLayer.frame = bounds` in `layoutSubviews()`.

### 2. Gesture Target Deallocated
**Problem**: Gesture recognizer stops working after view updates.
**Solution**: Use the Coordinator as the gesture target—it persists across updates.

### 3. Orientation Not Updating
**Problem**: Preview doesn't rotate with device.
**Solution**: Call `updatePreviewOrientation()` in both `layoutSubviews()` AND `updateUIView()`.

### 4. Thread Safety Violations
**Problem**: Crashes from wrong thread access.
**Solution**: Mark gesture handlers with `@MainActor`.

### 5. Using Deprecated API
**Problem**: `videoOrientation` property deprecated in iOS 17.
**Solution**: Use `videoRotationAngle` with `isVideoRotationAngleSupported(_:)` check.

### 6. Zoom Beyond Device Limits
**Problem**: Crashes when setting zoom factor outside valid range.
**Solution**: Clamp zoom between 1.0 and `device.maxAvailableVideoZoomFactor`.

## Best Practices Summary

| Aspect | Best Practice |
|--------|--------------|
| Layer Management | Custom UIView subclass with `didSet` observer |
| Frame Updates | Override `layoutSubviews()`, update layer frame |
| Gesture Handling | Coordinator as target, `@MainActor` annotations |
| Orientation | Use `videoRotationAngle` (iOS 17+) with support check |
| Thread Safety | `@MainActor` on all UI-touching methods |

## References

- [Apple: UIViewRepresentable Protocol](https://developer.apple.com/documentation/swiftui/uiviewrepresentable)
- [Apple: AVCaptureVideoPreviewLayer](https://developer.apple.com/documentation/avfoundation/avcapturevideopreviewlayer)
- [Apple: AVCaptureConnection.videoRotationAngle](https://developer.apple.com/documentation/avfoundation/avcaptureconnection/videorotationangle)

---
_Derived from CarSeet project - CameraPreviewView.swift_

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
