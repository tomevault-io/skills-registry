---
name: swift-post-quantum-security-ios26
description: >- Use when this capability is needed.
metadata:
  author: esaldgut
---

# Post-quantum security (iOS 26 CryptoKit + transport)

iOS 26 ships post-quantum cryptography in two layers. **Transport** is handled for you: quantum-secure
TLS 1.3 key exchange (hybrid X25519 + ML-KEM-768) is **enabled by default** for `URLSession` and the
`Network` framework — there is no per-request opt-in. **Application-layer** encryption is available
through CryptoKit's HPKE with the new `XWingMLKEM768X25519` ciphersuite, plus first-class `MLKEM768`,
`MLKEM1024`, `MLDSA65`, and `MLDSA87` types (with `SecureEnclave`-backed variants). This skill applies
those primitives correctly — and corrects the single most common myth: **`CryptoKit.timingSafeEqual`
does not exist.**

## When to invoke

- You're encrypting data at the application layer (stored payloads, sealed envelopes) and want a
  quantum-resistant scheme — reach for HPKE with `XWingMLKEM768X25519_SHA256_AES_GCM_256`.
- You're comparing secrets (OAuth `state`, CSRF tokens, MACs, capability strings) and need a
  **constant-time** comparison.
- You're pinning a server certificate or public key for an auth/API host.
- You're reviewing crypto code and see `==` on `Data`/`String` for a secret, a hand-rolled RSA box,
  or a reference to a `timingSafeEqual` symbol that doesn't compile.

**Announce on invoke:** "Using `swift-post-quantum-security-ios26` to apply iOS 26 PQ-CryptoKit and constant-time comparison per Apple's CryptoKit/Security docs."

Do **not** add an HPKE layer "to get post-quantum" on data that only travels over `URLSession` —
TLS 1.3 already gives you PQC in transit on iOS 26. Use HPKE for **data at rest** or end-to-end
payloads the server can't read, where defense-in-depth beyond transit actually buys something.

## The canonical APIs (verified)

| API | Signature / form (verified) | Use |
|---|---|---|
| PQ-TLS 1.3 | *(none — automatic)* | Using `URLSession`/`Network` gives hybrid X25519+ML-KEM-768 key exchange by default on iOS 26. Legacy `Secure Transport` is excluded. |
| `HPKE.Ciphersuite.XWingMLKEM768X25519_SHA256_AES_GCM_256` | `static let … : HPKE.Ciphersuite` | The post-quantum HPKE ciphersuite (iOS 26.0+). |
| `HPKE.Sender` | `init(recipientKey:ciphersuite:info:)` then `seal(_:)` / `seal(_:authenticating:)`; `.encapsulatedKey` | Encrypt one or more messages to a recipient public key (iOS 17.0+; PQ via the XWing ciphersuite). |
| `MLKEM768` / `MLKEM1024` | `MLKEM768.PrivateKey.generate() throws -> MLKEM768.PrivateKey`; `.publicKey` | Module-lattice KEM (NIST FIPS 203). `SecureEnclave.MLKEM768` keeps the key in the SE. |
| `MLDSA65` / `MLDSA87` | `MLDSA65.PrivateKey` / `.PublicKey` | Module-lattice digital signatures (NIST FIPS 204). `SecureEnclave.MLDSA65` variant exists. |
| `HMAC.isValidAuthenticationCode(_:authenticating:using:)` | `static func isValidAuthenticationCode<D>(_ authenticationCode: HMAC<H>.MAC, authenticating authenticatedData: D, using key: SymmetricKey) -> Bool where D: DataProtocol` | **Constant-time** MAC verification. The canonical secret-equality primitive. |
| `NSPinnedDomains` (Info.plist) | dict of domain → `NSPinnedCAIdentities` / `NSPinnedLeafIdentities` (+ `NSIncludesSubdomains`) | Declarative cert pinning for `URLSession` (iOS 14+). |
| `SecTrustEvaluateWithError(_:_:)` | `func SecTrustEvaluateWithError(_ trust: SecTrust, _ error: …) -> Bool` | Validate the server trust before SPKI pinning in a `URLSessionDelegate`. |

> `XWingMLKEM768X25519.PrivateKey` and its `PublicKey` are real iOS 26.0+ types, but the concrete
> type's no-argument `init()` / `generate()` are **not** documented at a stable URL — create the
> key via the initializer your CryptoKit version exposes (e.g. the documented
> `init(seedRepresentation:publicKey:)`) and treat the ciphersuite constant as the load-bearing
> public API to verify. `MLKEM768.PrivateKey.generate()` **is** documented and is the safe example.

## The rules (break them and the crypto breaks)

### 1. There is no `CryptoKit.timingSafeEqual` — never compare secrets with `==`

`==` on `String`/`Data` short-circuits on the first differing byte, leaking timing. CryptoKit ships
**no** `timingSafeEqual` symbol (a persistent myth). The canonical constant-time tools are:

- **MAC verification:** `HMAC<SHA256>.isValidAuthenticationCode(mac, authenticating: data, using: key)`.
- **Equal-length byte comparison:** `Data.elementsEqual(_:)` / `HashedAuthenticationCode.elementsEqual(_:)`.
- **Arbitrary equal-length secrets:** XOR-accumulate (below), or HMAC both sides with an ephemeral key and compare the MACs.

### 2. TLS 1.3 PQC is automatic — don't hand-roll it, don't claim a per-request API

On iOS 26, `URLSession` and `Network` negotiate hybrid X25519 + ML-KEM-768 automatically and fall
back gracefully when the peer lacks PQC. There is **no** opt-in flag and **no** support in the legacy
`Secure Transport` API. Just use `URLSession`. Reserve HPKE for app-layer/at-rest payloads.

### 3. HPKE app-layer encryption uses the XWing ciphersuite — not RSA, not raw ECDH

For quantum-resistant app-layer encryption, use `HPKE.Sender`/`HPKE.Recipient` with
`.XWingMLKEM768X25519_SHA256_AES_GCM_256`. Send the `encapsulatedKey` alongside the ciphertext; the
recipient reconstructs the same key schedule with its private key. Do not pin app-layer crypto to
RSA-2048 or bare ECDH for new code.

### 4. Pin certificates — declaratively where you can, by delegate where you need control

`NSPinnedDomains` in Info.plist covers `URLSession` from iOS 14+ (it does **not** cover
`SFSafariViewController`, and only covers `WKWebView` from iOS 16+). For SPKI pinning, rotation, or
backup-pin logic, implement `urlSession(_:didReceive:completionHandler:)`, call
`SecTrustEvaluateWithError` first, then hash the leaf's public key with SHA-256 and compare against
your pin set. `SecTrustCopyCertificateChain` requires iOS 15+.

### 5. Keep private keys in the Secure Enclave when the workload allows

For ML-KEM / ML-DSA keys that never need to leave the device, prefer `SecureEnclave.MLKEM768` /
`SecureEnclave.MLDSA65` so the private key material is generated and held in the SE; the Keychain
stores only a reference. Gate every PQ API behind `#available(iOS 26, *)`.

## Canonical example

Constant-time comparison plus an HPKE seal, using only verified APIs:

```swift
import CryptoKit
import Foundation

extension Data {
    /// Constant-time equality. Safe ONLY for equal-length secrets.
    /// There is no `CryptoKit.timingSafeEqual` — this is the fallback idiom.
    func constantTimeEquals(_ other: Data) -> Bool {
        guard count == other.count else { return false }
        var diff: UInt8 = 0
        for i in indices { diff |= self[i] ^ other[i] }
        return diff == 0
    }
}

/// Verify an OAuth `state` / CSRF token in constant time via HMAC (no `==` on secrets).
func stateMatches(received: Data, expected: Data, key: SymmetricKey) -> Bool {
    let expectedMAC = HMAC<SHA256>.authenticationCode(for: expected, using: key)
    return HMAC<SHA256>.isValidAuthenticationCode(expectedMAC, authenticating: received, using: key)
}

@available(iOS 26.0, macOS 26.0, *)
enum AppCrypto {
    static let suite: HPKE.Ciphersuite = .XWingMLKEM768X25519_SHA256_AES_GCM_256

    /// Encrypt a payload to a recipient's post-quantum public key.
    static func seal(_ payload: Data,
                     to recipient: XWingMLKEM768X25519.PublicKey,
                     info: Data) throws -> (encapsulated: Data, ciphertext: Data) {
        var sender = try HPKE.Sender(recipientKey: recipient, ciphersuite: suite, info: info)
        let ciphertext = try sender.seal(payload)
        return (sender.encapsulatedKey, ciphertext)
    }
}
```

SPKI pinning in a delegate (the control path):

```swift
final class PinningDelegate: NSObject, URLSessionDelegate {
    let pinnedSPKISHA256: Set<Data>
    init(pinnedSPKISHA256: Set<Data>) { self.pinnedSPKISHA256 = pinnedSPKISHA256 }

    func urlSession(_ session: URLSession,
                    didReceive challenge: URLAuthenticationChallenge,
                    completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void) {
        guard challenge.protectionSpace.authenticationMethod == NSURLAuthenticationMethodServerTrust,
              let trust = challenge.protectionSpace.serverTrust,
              SecTrustEvaluateWithError(trust, nil),                                  // validate first
              let chain = SecTrustCopyCertificateChain(trust) as? [SecCertificate],  // iOS 15+
              let leaf = chain.first,
              let key = SecCertificateCopyKey(leaf),
              let spki = SecKeyCopyExternalRepresentation(key, nil) as Data?
        else { return completionHandler(.cancelAuthenticationChallenge, nil) }

        let digest = Data(SHA256.hash(data: spki))
        completionHandler(pinnedSPKISHA256.contains(digest) ? .useCredential : .cancelAuthenticationChallenge,
                          pinnedSPKISHA256.contains(digest) ? URLCredential(trust: trust) : nil)
    }
}
```

## Decision aid: which primitive

- **In transit over HTTPS?** Nothing to do — `URLSession` on iOS 26 is already post-quantum.
- **At rest / end-to-end the server can't read?** HPKE + `XWingMLKEM768X25519_SHA256_AES_GCM_256`.
- **Comparing a secret/token/MAC?** `HMAC.isValidAuthenticationCode` (or XOR-accumulate on equal length). Never `==`.
- **Signing on-device with a non-exportable key?** `SecureEnclave.MLDSA65`.
- **Simple host pinning?** `NSPinnedDomains`. **Rotation / SPKI / backup pins?** delegate + `SecTrustEvaluateWithError`.

## Related skills

- `global-skills/apple-auth/swift-auth-security-checklist/SKILL.md` — where these primitives slot into
  the full storage/transport/lifecycle defense-in-depth checklist.
- `global-skills/apple-auth/swift-auth-security-audit-suite/SKILL.md` — the tests that assert the
  `state` comparison is constant-time and ATS has no arbitrary loads.
- `global-skills/apple/apple-anti-patterns/SKILL.md` — registers "compare secrets with `==`" and
  "invent a `timingSafeEqual`" as anti-patterns this skill avoids.

## Sources

- [HPKE](https://developer.apple.com/documentation/cryptokit/hpke) · [HPKE.Sender](https://developer.apple.com/documentation/cryptokit/hpke/sender) · [XWingMLKEM768X25519](https://developer.apple.com/documentation/cryptokit/xwingmlkem768x25519) · [MLKEM768](https://developer.apple.com/documentation/cryptokit/mlkem768) · [MLDSA65](https://developer.apple.com/documentation/cryptokit/mldsa65)
- [HMAC.isValidAuthenticationCode(_:authenticating:using:)](https://developer.apple.com/documentation/cryptokit/hmac/isvalidauthenticationcode(_:authenticating:using:)-8ezmw) · [HMAC](https://developer.apple.com/documentation/cryptokit/hmac)
- [NSPinnedDomains](https://developer.apple.com/documentation/bundleresources/information-property-list/nsapptransportsecurity/nspinneddomains) · [SecTrustEvaluateWithError](https://developer.apple.com/documentation/security/sectrustevaluatewitherror(_:_:)) · [SecCertificateCopyKey](https://developer.apple.com/documentation/security/seccertificatecopykey(_:))
- Apple Platform Security — [Quantum-secure cryptography in Apple operating systems](https://support.apple.com/guide/security/quantum-secure-cryptography-apple-devices-secc7c82e533/web) · WWDC25 [314 Get ahead with quantum-secure cryptography](https://developer.apple.com/videos/play/wwdc2025/314/)
- [RFC 9180 HPKE](https://datatracker.ietf.org/doc/html/rfc9180) · [RFC 8446 TLS 1.3](https://datatracker.ietf.org/doc/html/rfc8446) · [OWASP Pinning Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Pinning_Cheat_Sheet.html)

---

**Last verified:** 2026-06-03 against CryptoKit + Security (Apple Developer docs, live) — HPKE XWing
ciphersuite, `MLKEM768`/`MLDSA65`, and `HMAC.isValidAuthenticationCode` signatures confirmed iOS 26.0/13.0+.
**`CryptoKit.timingSafeEqual` was checked and does NOT exist** — use `HMAC.isValidAuthenticationCode`
or equal-length XOR-accumulate. `XWingMLKEM768X25519.PrivateKey()` / `.generate()` are not documented
at a stable URL; the ciphersuite constant and `MLKEM768.PrivateKey.generate()` are the verified anchors.
**Re-check after:** WWDC26 + any CryptoKit/Security release, or by 2026-12-01. **Decay risk:** medium.
**Found a drift?** Run `/skill-pattern-freshness-audit apple-auth`.

---
> Source: [esaldgut/ai-native-engineering-workspace](https://github.com/esaldgut/ai-native-engineering-workspace) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
