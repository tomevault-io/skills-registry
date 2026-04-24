---
name: flyingfox-mjpeg-server
description: HTTP server in Swift using FlyingFox to stream MJPEG video to web browsers including Tesla's limited browser. Use this skill when building actor-based HTTP servers, implementing JavaScript polling for browser compatibility, adding HTTP Basic Auth for PIN protection, or streaming live video with proper cache headers. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# FlyingFox MJPEG Server for iOS

## Overview

Build an HTTP server in Swift using FlyingFox to stream MJPEG video to web browsers, including Tesla's limited browser. This skill covers actor-based server architecture, JavaScript polling for maximum browser compatibility, HTTP Basic Auth for PIN protection, and proper cache headers for live streaming.

## Why JavaScript Polling (Not multipart/x-mixed-replace)

Traditional MJPEG streaming uses `multipart/x-mixed-replace` content type for server-push updates. However:

- **Tesla browser** does not support multipart streaming
- **Some older browsers** have inconsistent multipart support
- **JavaScript polling** works universally and gives client-side control over frame rate

The polling approach fetches individual JPEG frames via `/frame` endpoint, providing maximum compatibility.

## Dependencies

```swift
// Package.swift
dependencies: [
    .package(url: "https://github.com/swhitty/FlyingFox.git", from: "0.14.0")
]
```

## Core Architecture

### Actor-Based Server

Using Swift's `actor` ensures thread-safe state management:

```swift
import Foundation
import FlyingFox

actor MJPEGServer {
    private let port: UInt16
    private var server: HTTPServer?
    private var isRunning = false
    
    // Client tracking
    private var activeClients = Set<UUID>()
    
    // Frame provider closure - nonisolated so it can be set from MainActor
    nonisolated(unsafe) var frameProvider: (() -> Data?)?
    
    // Authentication provider - returns nil if auth disabled, or the required PIN
    nonisolated(unsafe) var authProvider: (() -> String?)?
    
    // FPS provider - returns current target FPS setting
    nonisolated(unsafe) var fpsProvider: (() -> Int)?
    
    init(port: UInt16 = 8080) {
        self.port = port
    }
    
    var clientCount: Int {
        activeClients.count
    }
}
```

### nonisolated(unsafe) Closures

The `nonisolated(unsafe)` attribute allows setting closures from the `@MainActor` context without async/await:

```swift
// In your view model or app initialization (MainActor)
await server.start()

// Set providers directly - no await needed due to nonisolated(unsafe)
server.frameProvider = { [weak self] in
    self?.cameraManager.latestJPEGFrame
}

server.authProvider = { [weak self] in
    self?.pinManager.currentPIN  // nil if PIN disabled
}

server.fpsProvider = { [weak self] in
    self?.settings.targetFPS ?? 20
}
```

## Route Implementation

### Starting the Server

```swift
func start() async throws {
    guard !isRunning else { return }
    
    server = HTTPServer(port: port)
    
    // Route: Root - HTML viewer page (or login redirect)
    await server?.appendRoute("GET /") { [weak self] request in
        guard let self = self else {
            return HTTPResponse(statusCode: .internalServerError)
        }
        
        // Check authentication
        if let authResult = await self.validateAuth(request) {
            return authResult  // Returns 401 if auth fails
        }
        
        let html = await self.generateViewerHTML()
        return HTTPResponse(
            statusCode: .ok,
            headers: [.contentType: "text/html; charset=utf-8"],
            body: Data(html.utf8)
        )
    }
    
    // Route: Single frame endpoint (primary streaming method)
    await server?.appendRoute("GET /frame") { [weak self] request in
        guard let self = self else {
            return HTTPResponse(statusCode: .internalServerError)
        }
        
        if let authResult = await self.validateAuth(request) {
            return authResult
        }
        
        return await self.handleFrameRequest(request)
    }
    
    // Route: Status endpoint (no auth required)
    await server?.appendRoute("GET /status") { [weak self] request in
        guard let self = self else {
            return HTTPResponse(statusCode: .internalServerError)
        }
        let status = await self.getStatusJSON()
        return HTTPResponse(
            statusCode: .ok,
            headers: [.contentType: "application/json"],
            body: Data(status.utf8)
        )
    }
    
    // Route: Settings endpoint - returns current target FPS
    await server?.appendRoute("GET /settings") { [weak self] request in
        guard let self = self else {
            return HTTPResponse(statusCode: .internalServerError)
        }
        let targetFPS = self.fpsProvider?() ?? 20
        let json = "{\"targetFPS\": \(targetFPS)}"
        return HTTPResponse(
            statusCode: .ok,
            headers: [
                .contentType: "application/json",
                HTTPHeader("Cache-Control"): "no-cache"
            ],
            body: Data(json.utf8)
        )
    }
    
    isRunning = true
    
    // Start server in background task
    Task {
        do {
            try await server?.run()
        } catch {
            print("Server error: \(error)")
            self.setRunning(false)
        }
    }
}

private func setRunning(_ value: Bool) {
    isRunning = value
}

func stop() async {
    await server?.stop()
    server = nil
    isRunning = false
    activeClients.removeAll()
}
```

### Frame Request Handler

```swift
private func handleFrameRequest(_ request: HTTPRequest) async -> HTTPResponse {
    guard let frame = frameProvider?() else {
        return HTTPResponse(statusCode: .serviceUnavailable)
    }
    
    return HTTPResponse(
        statusCode: .ok,
        headers: [
            .contentType: "image/jpeg",
            HTTPHeader("Cache-Control"): "no-cache, no-store, must-revalidate",
            HTTPHeader("Pragma"): "no-cache",
            HTTPHeader("Expires"): "0",
            HTTPHeader("Access-Control-Allow-Origin"): "*"
        ],
        body: frame
    )
}
```

### Critical Cache Headers

For live streaming, prevent any caching:

| Header | Value | Purpose |
|--------|-------|---------|
| `Cache-Control` | `no-cache, no-store, must-revalidate` | Prevent browser cache |
| `Pragma` | `no-cache` | HTTP/1.0 compatibility |
| `Expires` | `0` | Mark as immediately expired |
| `Access-Control-Allow-Origin` | `*` | Allow cross-origin requests |

## HTTP Basic Auth Implementation

### Authentication Validation

```swift
private func validateAuth(_ request: HTTPRequest) -> HTTPResponse? {
    // Check if authentication is enabled
    guard let requiredPIN = authProvider?() else {
        return nil  // No auth required
    }
    
    // Get Authorization header
    guard let authHeader = request.headers[HTTPHeader("Authorization")] else {
        return HTTPResponse(
            statusCode: .unauthorized,
            headers: [
                HTTPHeader("WWW-Authenticate"): "Basic realm=\"CarSeet Stream\"",
                .contentType: "text/html; charset=utf-8"
            ],
            body: Data(generateUnauthorizedHTML().utf8)
        )
    }
    
    // Validate Basic Auth format
    guard authHeader.hasPrefix("Basic ") else {
        return HTTPResponse(
            statusCode: .unauthorized,
            headers: [
                HTTPHeader("WWW-Authenticate"): "Basic realm=\"CarSeet Stream\""
            ],
            body: Data()
        )
    }
    
    // Decode Base64 credentials
    let base64Credentials = String(authHeader.dropFirst(6))
    guard let credentialsData = Data(base64Encoded: base64Credentials),
          let credentials = String(data: credentialsData, encoding: .utf8) else {
        return HTTPResponse(statusCode: .unauthorized)
    }
    
    // Format: "username:password"
    let parts = credentials.split(separator: ":", maxSplits: 1)
    guard parts.count == 2 else {
        return HTTPResponse(statusCode: .unauthorized)
    }
    
    let username = String(parts[0])
    let password = String(parts[1])
    
    // Validate credentials (fixed username, PIN as password)
    guard username == "carseet" && password == requiredPIN else {
        return HTTPResponse(
            statusCode: .unauthorized,
            headers: [
                HTTPHeader("WWW-Authenticate"): "Basic realm=\"CarSeet Stream\""
            ],
            body: Data(generateUnauthorizedHTML().utf8)
        )
    }
    
    return nil  // Auth successful
}
```

### Security Considerations

1. **Use HTTPS in production** - Basic Auth transmits Base64-encoded credentials (not encrypted)
2. **Consider constant-time comparison** for PIN validation to prevent timing attacks
3. **Rate limit login attempts** to prevent brute force attacks

## JavaScript Polling Viewer

### HTML/JavaScript Client

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>CarSeet Stream</title>
    <style>
        html, body {
            width: 100%;
            height: 100%;
            background: #000;
            overflow: hidden;
            margin: 0;
        }
        #stream {
            max-width: 100%;
            max-height: 100%;
            object-fit: contain;
        }
        .fps-counter {
            position: absolute;
            top: 16px;
            right: 16px;
            padding: 8px 16px;
            background: rgba(0, 0, 0, 0.6);
            border-radius: 8px;
            color: white;
            font-size: 12px;
            font-family: monospace;
        }
    </style>
</head>
<body>
    <img id="stream" alt="Camera Stream">
    <div id="fps-counter" class="fps-counter">-- fps</div>
    
    <script>
        const stream = document.getElementById('stream');
        const fpsCounter = document.getElementById('fps-counter');
        
        let frameCount = 0;
        let lastFpsUpdate = Date.now();
        let targetFPS = 20;
        let frameInterval = 1000 / targetFPS;
        let consecutiveErrors = 0;
        const MAX_ERRORS = 10;
        
        // Get auth credentials from sessionStorage
        const authCredentials = sessionStorage.getItem('carseet_auth');
        
        // Fetch target FPS from server
        async function updateTargetFPS() {
            try {
                const response = await fetch('/settings');
                if (response.ok) {
                    const settings = await response.json();
                    if (settings.targetFPS && settings.targetFPS !== targetFPS) {
                        targetFPS = settings.targetFPS;
                        frameInterval = 1000 / targetFPS;
                    }
                }
            } catch (e) {
                console.log('Could not fetch settings');
            }
        }
        
        // Update FPS every 5 seconds
        setInterval(updateTargetFPS, 5000);
        
        // Update FPS counter display
        function updateFps() {
            const now = Date.now();
            const elapsed = now - lastFpsUpdate;
            if (elapsed >= 1000) {
                const fps = Math.round((frameCount * 1000) / elapsed);
                fpsCounter.textContent = fps + ' fps';
                frameCount = 0;
                lastFpsUpdate = now;
            }
        }
        
        // Fetch a single frame
        async function fetchFrame() {
            try {
                const headers = {};
                if (authCredentials) {
                    headers['Authorization'] = 'Basic ' + authCredentials;
                }
                
                // Cache-busting query parameter
                const response = await fetch('/frame?' + Date.now(), {
                    cache: 'no-store',
                    headers: headers
                });
                
                // Handle auth failure
                if (response.status === 401) {
                    sessionStorage.removeItem('carseet_auth');
                    window.location.href = '/login';
                    return false;
                }
                
                if (!response.ok) {
                    throw new Error('Frame fetch failed');
                }
                
                const blob = await response.blob();
                const url = URL.createObjectURL(blob);
                
                // Revoke old URL to prevent memory leak
                if (stream.src && stream.src.startsWith('blob:')) {
                    URL.revokeObjectURL(stream.src);
                }
                
                stream.src = url;
                frameCount++;
                consecutiveErrors = 0;
                updateFps();
                return true;
            } catch (error) {
                consecutiveErrors++;
                console.error('Frame error:', error);
                return consecutiveErrors < MAX_ERRORS;
            }
        }
        
        // Main streaming loop
        async function startStreaming() {
            await updateTargetFPS();
            
            while (true) {
                const startTime = Date.now();
                const shouldContinue = await fetchFrame();
                
                if (!shouldContinue) {
                    break;
                }
                
                // Maintain target frame rate
                const elapsed = Date.now() - startTime;
                const delay = Math.max(0, frameInterval - elapsed);
                await new Promise(resolve => setTimeout(resolve, delay));
            }
        }
        
        // Prevent screen timeout on mobile
        if ('wakeLock' in navigator) {
            navigator.wakeLock.request('screen').catch(() => {});
        }
        
        // Handle tab visibility changes
        document.addEventListener('visibilitychange', function() {
            if (document.visibilityState === 'visible') {
                consecutiveErrors = 0;
                startStreaming();
            }
        });
        
        // Start on load
        startStreaming();
    </script>
</body>
</html>
```

### Key JavaScript Patterns

1. **Memory Management**: Always `URL.revokeObjectURL()` old blob URLs
2. **Cache Busting**: Add timestamp query parameter to prevent caching
3. **Error Recovery**: Track consecutive errors, reconnect on tab focus
4. **Dynamic FPS**: Fetch target FPS from `/settings` endpoint periodically
5. **Wake Lock**: Prevent screen timeout during viewing

## Login Page with PIN Entry

```html
<div class="login-container">
    <h1>CarSeet</h1>
    <form id="loginForm">
        <input type="password" id="pin" 
               placeholder="Enter PIN" 
               inputmode="numeric" 
               pattern="[0-9]*"
               required>
        <button type="submit">View Stream</button>
    </form>
</div>

<script>
    const form = document.getElementById('loginForm');
    
    form.addEventListener('submit', async (e) => {
        e.preventDefault();
        
        const pin = document.getElementById('pin').value;
        const credentials = btoa('carseet:' + pin);
        
        try {
            // Test credentials against /frame endpoint
            const response = await fetch('/frame', {
                headers: { 'Authorization': 'Basic ' + credentials }
            });
            
            if (response.ok) {
                // Store credentials and redirect
                sessionStorage.setItem('carseet_auth', credentials);
                window.location.href = '/?auth=' + credentials;
            } else {
                alert('Invalid PIN');
            }
        } catch (err) {
            alert('Connection error');
        }
    });
</script>
```

## Integration with Camera Manager

### Frame Provider Setup

```swift
@MainActor
class StreamingService: ObservableObject {
    private let server = MJPEGServer(port: 8080)
    private let cameraManager: CameraManager
    private let pinManager: PINManager
    
    @Published var targetFPS: Int = 20 {
        didSet {
            // FPS provider closure automatically uses this value
        }
    }
    
    init(cameraManager: CameraManager, pinManager: PINManager) {
        self.cameraManager = cameraManager
        self.pinManager = pinManager
        
        // Set up frame provider
        server.frameProvider = { [weak self] in
            self?.cameraManager.latestJPEGFrame
        }
        
        // Set up auth provider
        server.authProvider = { [weak self] in
            self?.pinManager.currentPIN  // nil if disabled
        }
        
        // Set up FPS provider
        server.fpsProvider = { [weak self] in
            self?.targetFPS ?? 20
        }
    }
    
    func startStreaming() async throws {
        try await server.start()
    }
    
    func stopStreaming() async {
        await server.stop()
    }
}
```

### Camera Manager JPEG Output

```swift
class CameraManager: NSObject, ObservableObject {
    @Published var latestJPEGFrame: Data?
    
    func captureOutput(_ output: AVCaptureOutput, 
                       didOutput sampleBuffer: CMSampleBuffer, 
                       from connection: AVCaptureConnection) {
        guard let imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer) else { return }
        
        let ciImage = CIImage(cvPixelBuffer: imageBuffer)
        let context = CIContext()
        
        guard let cgImage = context.createCGImage(ciImage, from: ciImage.extent) else { return }
        
        let uiImage = UIImage(cgImage: cgImage)
        
        // Compress to JPEG with quality balance (0.7 = good quality, reasonable size)
        if let jpegData = uiImage.jpegData(compressionQuality: 0.7) {
            DispatchQueue.main.async {
                self.latestJPEGFrame = jpegData
            }
        }
    }
}
```

## Tesla Browser Compatibility Notes

1. **No multipart/x-mixed-replace support** - Must use polling
2. **Limited JavaScript features** - Keep code simple
3. **No WebSocket support in older firmware** - HTTP polling is reliable
4. **Touch events** - Use standard click handlers
5. **Viewport** - Use `user-scalable=no` to prevent zoom issues

## Error Handling

### Server-Side Errors

```swift
private func handleFrameRequest(_ request: HTTPRequest) async -> HTTPResponse {
    // No frame provider configured
    guard let provider = frameProvider else {
        return HTTPResponse(
            statusCode: .internalServerError,
            body: Data("{\"error\": \"Frame provider not configured\"}".utf8)
        )
    }
    
    // Frame not available (camera not started, etc.)
    guard let frame = provider() else {
        return HTTPResponse(
            statusCode: .serviceUnavailable,
            headers: [
                .contentType: "application/json",
                HTTPHeader("Retry-After"): "1"
            ],
            body: Data("{\"error\": \"No frame available\"}".utf8)
        )
    }
    
    return HTTPResponse(
        statusCode: .ok,
        headers: [.contentType: "image/jpeg"],
        body: frame
    )
}
```

### Client-Side Error Recovery

```javascript
// Exponential backoff for reconnection
let retryDelay = 1000;
const MAX_RETRY_DELAY = 30000;

async function fetchFrameWithRetry() {
    try {
        const success = await fetchFrame();
        if (success) {
            retryDelay = 1000;  // Reset on success
        }
        return success;
    } catch (error) {
        retryDelay = Math.min(retryDelay * 2, MAX_RETRY_DELAY);
        await new Promise(resolve => setTimeout(resolve, retryDelay));
        return true;  // Keep trying
    }
}
```

## Performance Optimization

### Frame Size Considerations

| Resolution | JPEG Quality | Typical Size | FPS Target |
|------------|--------------|--------------|------------|
| 1920x1080 | 0.7 | 100-200 KB | 15-20 |
| 1280x720 | 0.7 | 50-100 KB | 20-30 |
| 640x480 | 0.7 | 20-40 KB | 30+ |

### Bandwidth Calculation

```
Bandwidth = Frame Size × FPS

Example: 100 KB × 20 FPS = 2 MB/s = 16 Mbps
```

### Adaptive Quality

```swift
var jpegQuality: CGFloat {
    switch targetFPS {
    case 25...: return 0.5  // Lower quality for high FPS
    case 15...: return 0.7  // Balanced
    default: return 0.85    // High quality for low FPS
    }
}
```

## Testing

### Manual Testing

1. Start server and open `http://[device-ip]:8080` in browser
2. Verify frame updates at target FPS
3. Test PIN authentication flow
4. Test on Tesla browser specifically

### Automated Testing

```swift
func testFrameEndpointReturnsJPEG() async throws {
    let server = MJPEGServer(port: 8081)
    server.frameProvider = { testJPEGData }
    
    try await server.start()
    defer { Task { await server.stop() } }
    
    let url = URL(string: "http://localhost:8081/frame")!
    let (data, response) = try await URLSession.shared.data(from: url)
    
    let httpResponse = response as! HTTPURLResponse
    XCTAssertEqual(httpResponse.statusCode, 200)
    XCTAssertEqual(httpResponse.value(forHTTPHeaderField: "Content-Type"), "image/jpeg")
    XCTAssertFalse(data.isEmpty)
}
```

## Reference Implementation

See `CarSeet/CarSeet/MJPEGServer.swift` for the complete production implementation including:

- Full HTML/CSS/JS viewer with dark theme
- Login page with PIN entry
- Error screens with retry buttons
- Status overlay with live indicator
- FPS counter display

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
