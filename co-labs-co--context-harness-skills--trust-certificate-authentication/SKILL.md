---
name: trust-certificate-authentication
description: Secure device-to-device trust relationships using cryptographic certificates on iOS. Use this skill when establishing trusted P2P connections, implementing device authentication without servers, building QR code pairing flows, or creating persistent trust relationships stored in Keychain. Use when this capability is needed.
metadata:
  author: co-labs-co
---

# Skill: Trust Certificate Authentication (iOS/Swift)

Implements secure device-to-device trust relationships using cryptographic certificates on iOS. Covers secure token generation, Keychain storage with appropriate accessibility levels, QR code exchange for trust establishment, bidirectional trust models, and timing-attack-resistant certificate validation.

## When to Use

- Establishing trusted peer-to-peer connections between iOS devices
- Implementing device authentication without a central server
- Building secure pairing flows using QR codes
- Creating persistent trust relationships that survive app restarts
- Protecting authentication flows from timing attacks

## Key Concepts

### Keychain Accessibility Levels

| Level | When Accessible | iCloud Sync | Use Case |
|-------|-----------------|-------------|----------|
| `.whenUnlocked` | Device unlocked | Yes | General secrets (synced) |
| `.afterFirstUnlock` | After first unlock | Yes | Background app tokens |
| `.whenUnlockedThisDeviceOnly` | Device unlocked | No | **Device-specific credentials** |
| `.afterFirstUnlockThisDeviceOnly` | After first unlock | No | Background + device-only |

**Recommendation**: Use `.whenUnlockedThisDeviceOnly` for trust certificates.

### Bidirectional Trust Model

```
Device A                          Device B
┌──────────────┐                  ┌──────────────┐
│ TrustedDevices: │              │ TrustedDevices: │
│  - Device B  │  ←── mutual ──→ │  - Device A  │
│    (cert_B)  │      trust      │    (cert_A)  │
└──────────────┘                  └──────────────┘

Either device can initiate connection.
Both store each other's certificates.
```

### Constant-Time Comparison

Prevents timing attacks by ensuring comparison time is independent of match position:

```swift
// ❌ DANGEROUS: Early exit leaks timing information
func vulnerableCompare(_ a: String, _ b: String) -> Bool {
    for (charA, charB) in zip(a, b) {
        if charA != charB { return false }  // Early exit!
    }
    return true
}

// ✅ SECURE: Constant-time comparison
func constantTimeCompare(_ a: String, _ b: String) -> Bool {
    let aData = Data(a.utf8)
    let bData = Data(b.utf8)
    
    guard aData.count == bData.count else { return false }
    
    var result: UInt8 = 0
    for (aByte, bByte) in zip(aData, bData) {
        result |= aByte ^ bByte  // XOR accumulates differences
    }
    return result == 0  // Only true if ALL bytes matched
}
```

## Implementation Guide

### Step 1: Set Up KeychainAccess

```swift
import KeychainAccess

final class TrustCertificateManager {
    private let keychain: Keychain
    
    init(service: String = Bundle.main.bundleIdentifier ?? "com.app") {
        self.keychain = Keychain(service: service)
            .accessibility(.whenUnlockedThisDeviceOnly)
            .label("App Trust Certificates")
    }
}
```

### Step 2: Generate Secure Tokens

```swift
import Security

func generateSecureToken() -> String? {
    var bytes = [UInt8](repeating: 0, count: 32)
    let status = SecRandomCopyBytes(kSecRandomDefault, bytes.count, &bytes)
    
    guard status == errSecSuccess else { return nil }
    
    return Data(bytes).base64EncodedString()
}
```

### Step 3: Define Trust Models

```swift
struct TrustedDevice: Codable, Identifiable {
    let deviceId: String
    let deviceName: String
    let certificate: String      // Shared secret (base64)
    let trustedAt: Date
    var lastConnected: Date?
    
    var id: String { deviceId }
}

struct TrustInvitation: Codable {
    let version: Int
    let deviceId: String
    let deviceName: String
    let certificate: String
    
    static let currentVersion = 2
}
```

### Step 4: Generate Trust Invitations

```swift
func generateTrustInvitation() -> TrustInvitation? {
    guard let certificate = generateSecureToken() else { return nil }
    
    return TrustInvitation(
        version: TrustInvitation.currentVersion,
        deviceId: deviceId,
        deviceName: deviceName,
        certificate: certificate
    )
}

static func encodeInvitation(_ invitation: TrustInvitation) -> String? {
    guard let jsonData = try? JSONEncoder().encode(invitation),
          let base64 = jsonData.base64EncodedString()
              .addingPercentEncoding(withAllowedCharacters: .urlPathAllowed) else {
        return nil
    }
    return "myapp://trust/" + base64
}
```

### Step 5: Validate Credentials (Constant-Time)

```swift
func validateDeviceCredential(_ credential: DeviceCredential) -> Bool {
    guard let trusted = trustedDevices.first(where: { 
        $0.deviceId == credential.deviceId 
    }) else {
        return false
    }
    
    // CRITICAL: Use constant-time comparison
    return constantTimeCompare(credential.certificate, trusted.certificate)
}

private func constantTimeCompare(_ a: String, _ b: String) -> Bool {
    let aData = Data(a.utf8)
    let bData = Data(b.utf8)
    
    guard aData.count == bData.count else { return false }
    
    var result: UInt8 = 0
    for (aByte, bByte) in zip(aData, bData) {
        result |= aByte ^ bByte
    }
    return result == 0
}
```

### Step 6: QR Code Generation (Optimized)

```swift
import CoreImage
import Metal

/// Reusable Metal-backed CIContext (expensive to create)
private lazy var ciContext: CIContext = {
    if let device = MTLCreateSystemDefaultDevice() {
        return CIContext(mtlDevice: device, options: [.cacheIntermediates: true])
    }
    return CIContext(options: [.useSoftwareRenderer: false])
}()

@MainActor
func generateQRCodeAsync(for invitation: TrustInvitation) async -> UIImage? {
    guard let payload = Self.encodeInvitation(invitation) else { return nil }
    
    return await Task.detached(priority: .userInitiated) {
        let filter = CIFilter.qrCodeGenerator()
        filter.setValue(payload.data(using: .ascii), forKey: "inputMessage")
        
        guard let outputImage = filter.outputImage else { return nil }
        
        let scale: CGFloat = 8
        let scaled = outputImage.transformed(by: CGAffineTransform(scaleX: scale, y: scale))
        
        guard let cgImage = self.ciContext.createCGImage(scaled, from: scaled.extent) else {
            return nil
        }
        
        return UIImage(cgImage: cgImage)
    }.value
}
```

## Common Pitfalls

### 1. Using Standard String Comparison for Secrets
```swift
// ❌ WRONG: Vulnerable to timing attacks
if credential.certificate == trusted.certificate { ... }

// ✅ CORRECT: Constant-time comparison
constantTimeCompare(credential.certificate, trusted.certificate)
```

### 2. Weak Keychain Accessibility
```swift
// ❌ WRONG: Data syncs to iCloud
.accessibility(.afterFirstUnlock)

// ✅ CORRECT: Device-specific, no sync
.accessibility(.whenUnlockedThisDeviceOnly)
```

### 3. Creating CIContext Per QR Code
```swift
// ❌ WRONG: CIContext creation is expensive (~100-300ms)
func generateQR() {
    let context = CIContext()  // Created each time!
}

// ✅ CORRECT: Lazy-initialized, reused
private lazy var ciContext: CIContext = { ... }()
```

### 4. Storing Plain Secrets in UserDefaults
```swift
// ❌ WRONG: UserDefaults is not encrypted
UserDefaults.standard.set(certificate, forKey: "cert")

// ✅ CORRECT: Always use Keychain
keychain.set(certificate, key: "cert")
```

## References

- [Keychain Services - Apple Developer](https://developer.apple.com/documentation/security/keychain_services)
- [KeychainAccess Library](https://github.com/kishikawakatsumi/KeychainAccess)
- [Constant-Time Cryptography Guide](https://www.chosenplaintext.ca/articles/beginners-guide-constant-time-cryptography.html)

---
_Derived from CarSeet project - TrustCertificateManager.swift_

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/co-labs-co) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
