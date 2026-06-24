---
name: macios-header-analysis
description: Analyze Objective-C and Swift framework headers to extract the complete API surface for .NET binding generation. Use when you need to read xcframework headers, identify classes/protocols/enums/constants/blocks/delegates, determine inter-framework dependencies, map ObjC/Swift declarations to C# binding patterns, and produce a structured API inventory before writing ApiDefinition.cs and StructsAndEnums.cs. Use when this capability is needed.
metadata:
  author: dalexsoto
---

# Header Analysis for .NET Bindings

Systematically analyze Objective-C and Swift framework headers to extract the full API surface needed for .NET binding projects.

## When to Use This Skill

- Before creating a new binding project — to understand what APIs exist
- When binding a multi-framework SDK — to determine dependency order
- When updating bindings for a new SDK version — to find added/removed/changed APIs
- When deciding what to bind vs skip — to assess API complexity

## Quick Start

### 1. Extract Framework Metadata

Use the `analyze_xcframework.py` script (from `macios-binding-project` skill) or manually inspect:

```bash
# List all headers in the framework
FRAMEWORK="MyFramework.xcframework"
SLICE=$(ls "$FRAMEWORK" | grep "ios-arm64" | head -1)
HEADERS="$FRAMEWORK/$SLICE/MyFramework.framework/Headers"
ls "$HEADERS"
```

Key things to note:
- **`*-Swift.h`** — Auto-generated ObjC header for Swift code. Contains all `@objc`-exposed Swift APIs
- **Umbrella header** (e.g., `MyFramework.h`) — Imports all public headers
- **Individual `.h` files** — One per class/protocol typically

### 2. Classify the Framework

| Pattern | Meaning | Binding Approach |
|---|---|---|
| Only `*-Swift.h` | Pure Swift framework | Bind from the generated ObjC header only |
| Many `.h` files, no `-Swift.h` | Pure ObjC framework | Bind from individual headers |
| Both `.h` files and `-Swift.h` | Mixed ObjC + Swift | Bind ObjC headers + Swift-generated header |
| Very few headers | Minimal public API | Quick binding, check for protocols |

### 3. Read All Public Headers

Read every header file systematically. This is the most time-consuming but most important step.

```bash
# Read all headers
for h in "$HEADERS"/*.h; do
  echo "=== $(basename $h) ==="
  cat "$h"
done
```

### 4. Extract API Inventory

As you read, build a structured inventory (see detailed patterns below).

### 5. Determine Dependencies

Check `#import` statements and framework references to build the dependency graph.

## API Extraction Patterns

### Classes

Look for `@interface` declarations:

```objc
// Standard class
@interface FBSDKAccessToken : NSObject
// → [BaseType (typeof (NSObject))]
//   interface AccessToken { }

// Class with a specific ObjC name (Swift)
SWIFT_CLASS_NAMED("AccessToken")
@interface FBSDKAccessToken : NSObject
// → [BaseType (typeof (NSObject), Name = "FBSDKAccessToken")]
//   interface AccessToken { }

// Class inheriting from another bound class
@interface FBSDKLoginButton : FBButton
// → [BaseType (typeof (FBButton))]
//   interface LoginButton { }
```

**Record for each class:**
- ObjC class name (e.g., `FBSDKAccessToken`)
- C# name to use (e.g., `AccessToken` — strip prefix)
- Base class
- Adopted protocols (e.g., `<NSCopying, NSSecureCoding>`)
- Whether it's Swift-generated (`SWIFT_CLASS` / `SWIFT_CLASS_NAMED`)

### Protocols

Look for `@protocol` declarations:

```objc
// Protocol with required + optional methods
@protocol FBSDKSharingDelegate <NSObject>
@required
- (void)sharer:(id<FBSDKSharing>)sharer didCompleteWithResults:(NSDictionary<NSString *, id> *)results;
@optional
- (void)sharerDidCancel:(id<FBSDKSharing>)sharer;
@end
```

**Record for each protocol:**
- Protocol name
- Whether it has `@required` methods (→ `[Abstract]`)
- Whether it has `@optional` methods
- Whether it's used as a delegate (→ `[Protocol, Model]`)
- Whether it's used as a type constraint only (→ `[Protocol]` without `[Model]`)
- Parent protocols

**Delegate vs Type-constraint heuristic:**
- If a class has a `delegate` property typed as this protocol → it's a delegate, use `[Model]`
- If it's used as a generic constraint or return type → it's a type constraint, no `[Model]`
- If unsure → use `[Model]` (safer, generates both interface and abstract class)

### Properties

```objc
// Read-only property
@property (nonatomic, readonly, copy) NSString *tokenString;
// → [Export ("tokenString")] string TokenString { get; }

// Read-write property
@property (nonatomic, strong) NSString *name;
// → [Export ("name", ArgumentSemantic.Strong)] string Name { get; set; }

// Nullable property
@property (nonatomic, copy, nullable) NSString *email;
// → [NullAllowed] [Export ("email", ArgumentSemantic.Copy)] string Email { get; set; }

// Class property (static)
@property (class, nonatomic, readonly) FBSDKAccessToken *currentAccessToken;
// → [Static] [NullAllowed] [Export ("currentAccessToken")] AccessToken CurrentAccessToken { get; }
```

**Property attribute mapping:**

| ObjC Attribute | C# Equivalent |
|---|---|
| `readonly` | Only `get;` accessor |
| `readwrite` (default) | Both `get; set;` |
| `copy` | `ArgumentSemantic.Copy` |
| `strong` / `retain` | `ArgumentSemantic.Strong` |
| `weak` | `ArgumentSemantic.Weak` (+ `[NullAllowed]`) |
| `assign` | `ArgumentSemantic.Assign` (value types) |
| `nullable` | `[NullAllowed]` |
| `nonnull` | No attribute needed (default) |
| `class` | `[Static]` |
| `nonatomic` | No C# equivalent needed |

### Methods

```objc
// Instance method, no params
- (void)logOut;
// → [Export ("logOut")] void LogOut ();

// Instance method with params
- (void)logInWithPermissions:(NSArray<NSString *> *)permissions
          fromViewController:(UIViewController *)viewController
                     handler:(FBSDKLoginManagerLoginResultBlock)handler;
// → [Export ("logInWithPermissions:fromViewController:handler:")]
//   void LogIn (string[] permissions, [NullAllowed] UIViewController viewController,
//               Action<LoginManagerLoginResult, NSError> handler);

// Class method (static)
+ (instancetype)sharedInstance;
// → [Static] [Export ("sharedInstance")] MyClass SharedInstance { get; }

// Factory method — CAUTION: may not exist at runtime for Swift classes
+ (instancetype)tokenWithString:(NSString *)string;
// → Prefer binding as constructor: [Export ("initWithString:")] NativeHandle Constructor (string str);

// Failable initializer
- (nullable instancetype)initWithURL:(NSURL *)url;
// → [Export ("initWithURL:")] NativeHandle Constructor (NSUrl url);
//   (add [return: NullAllowed] if failable)
```

**Method selector construction:**
- No params: method name only → `"logOut"`
- One param: method name + `:` → `"initWithString:"`
- Multiple params: first part + `:` + subsequent labels + `:` → `"logInWithPermissions:fromViewController:handler:"`

### Enums

```objc
// NS_ENUM — standard enum
typedef NS_ENUM(NSInteger, FBSDKLoginTracking) {
    FBSDKLoginTrackingEnabled,
    FBSDKLoginTrackingLimited,
};
// → [Native]
//   public enum LoginTracking : long {
//       Enabled = 0,
//       Limited = 1,
//   }

// NS_OPTIONS — flags enum
typedef NS_OPTIONS(NSUInteger, FBSDKGameRequestFilter) {
    FBSDKGameRequestFilterNone      = 0,
    FBSDKGameRequestFilterAppUsers  = 1 << 0,
    FBSDKGameRequestFilterAppNonUsers = 1 << 1,
};
// → [Native] [Flags]
//   public enum GameRequestFilter : ulong {
//       None = 0, AppUsers = 1, AppNonUsers = 2,
//   }

// NS_CLOSED_ENUM — exhaustive enum
typedef NS_CLOSED_ENUM(NSInteger, FBSDKAppEventName) { ... };
// → Same as NS_ENUM but guarantees no new cases in future
```

**Enum naming convention:** Strip the common prefix. `FBSDKLoginTrackingEnabled` → `Enabled`.

### Constants and Fields

```objc
// String constant
FOUNDATION_EXPORT NSString *const FBSDKErrorDomain;
// → [Field ("FBSDKErrorDomain", "FrameworkName")]
//   NSString ErrorDomain { get; }

// Notification name
FOUNDATION_EXPORT NSNotificationName const FBSDKAccessTokenDidChangeNotification;
// → [Notification]
//   [Field ("FBSDKAccessTokenDidChangeNotification", "FrameworkName")]
//   NSString DidChangeNotification { get; }

// Numeric constant
FOUNDATION_EXPORT const NSInteger FBSDKErrorCodeNetwork;
// → [Field ("FBSDKErrorCodeNetwork", "FrameworkName")]
//   nint ErrorCodeNetwork { get; }
```

**Critical:** The library parameter must be the **framework name** (e.g., `"FBSDKCoreKit"`), not `"__Internal"`.

### Blocks (Callbacks)

```objc
typedef void (^FBSDKLoginManagerLoginResultBlock)(FBSDKLoginManagerLoginResult *_Nullable result,
                                                   NSError *_Nullable error);
// → delegate void LoginManagerLoginResultHandler (
//       [NullAllowed] LoginManagerLoginResult result,
//       [NullAllowed] NSError error);

// Or inline as Action/Func:
// → Action<LoginManagerLoginResult, NSError> handler
```

**Block mapping rules:**
- `void (^)(void)` → `Action`
- `void (^)(Type1, Type2)` → `Action<Type1, Type2>` or named delegate
- `ReturnType (^)(Type1)` → `Func<Type1, ReturnType>` or named delegate
- Complex blocks with nullable params → use named delegate for clarity

### Categories (Extensions)

```objc
@interface NSString (MyFrameworkAdditions)
- (NSString *)myFramework_urlEncode;
@end
// → [Category]
//   [BaseType (typeof (NSString))]
//   interface NSStringMyFrameworkAdditions {
//       [Export ("myFramework_urlEncode")]
//       string UrlEncode ();
//   }
```

## Dependency Analysis

### Finding Framework Dependencies

Check these sources for import relationships:

```bash
# 1. Check umbrella header imports
cat "$HEADERS/MyFramework.h"

# 2. Search all headers for imports of other frameworks
grep -r "#import <" "$HEADERS/" | grep -v "MyFramework/" | sort -u

# 3. Check module map for dependencies
cat "$FRAMEWORK/$SLICE/MyFramework.framework/Modules/module.modulemap"

# 4. Check linked frameworks in binary (if needed)
otool -L "$FRAMEWORK/$SLICE/MyFramework.framework/MyFramework" 2>/dev/null | grep -v "/usr/lib"
```

### Building the Dependency Graph

For a multi-framework SDK:

1. **List all frameworks** in the SDK
2. **For each framework**, find which other SDK frameworks it imports
3. **Build a DAG** (directed acyclic graph) of dependencies
4. **Determine build order** — bind leaf frameworks first (those with no SDK dependencies)

Example (Facebook SDK):
```
FBSDKCoreKit_Basics (no SDK deps) ─┐
FBAEMKit (no SDK deps) ────────────┤
                                    ├─→ FBSDKCoreKit ─┬─→ FBSDKLoginKit
                                                      ├─→ FBSDKShareKit ──→ FBSDKGamingServicesKit
                                                      └───────────────────┘
```

**Binding order:** Basics, AEMKit → CoreKit → LoginKit, ShareKit → GamingServicesKit

### Cross-Framework Type References

When Framework B depends on Framework A:
- Types from A used in B's API must be bound in A's project first
- B's binding project needs a `<ProjectReference>` to A's binding project
- **Do not re-declare** types from A in B's binding — use `using` imports

## SDK Version Diff Workflow

When updating bindings for a new SDK version:

### 1. Identify Changes

```bash
# Diff headers between old and new versions
diff -r OldFramework.xcframework/ios-arm64/OldFramework.framework/Headers \
        NewFramework.xcframework/ios-arm64/NewFramework.framework/Headers
```

### 2. Categorize Changes

| Change Type | Action |
|---|---|
| New class | Add new `[BaseType]` interface |
| New method on existing class | Add `[Export]` to existing interface |
| New property | Add property to existing interface |
| New enum value | Add to existing enum |
| Removed API | Add `[Obsolete]` or remove (breaking change) |
| Changed parameter type | Update binding signature |
| New protocol | Add `[Protocol, Model]` interface |
| Renamed API | Keep old with `[Obsolete]`, add new |

### 3. Handle Deprecations

```csharp
// API deprecated in header with __deprecated_msg("Use newMethod instead")
[Deprecated (PlatformName.iOS, 16, 0, message: "Use 'NewMethod' instead.")]
[Export ("oldMethod")]
void OldMethod ();
```

## API Inventory Template

Use this structure to document extracted APIs before writing bindings:

```
## Framework: MyFramework
Type: Mixed ObjC + Swift
Headers: 15 total (14 ObjC + 1 Swift-generated)
Dependencies: Foundation, UIKit, CoreGraphics, OtherSDKFramework

### Classes (8)
1. MyManager — NSObject, singleton, main entry point
   - Properties: sharedInstance (static), isConfigured (readonly), delegate (weak)
   - Methods: configure(options:), reset(), logEvent(name:params:)
   - Constructors: none (use sharedInstance)
   - Notes: [DisableDefaultCtor]

2. MyConfiguration — NSObject, data class
   - Properties: appId, appSecret, environment
   - Constructors: initWithAppId:appSecret:
   - Protocols: NSCopying, NSSecureCoding

### Protocols (3)
1. MyManagerDelegate — delegate pattern
   - Required: managerDidFinish(manager:result:)
   - Optional: managerDidFail(manager:error:)

2. MyConfigurable — type constraint (used as <MyConfigurable> param type)
   - Required: configure(with:)

### Enums (4)
1. MyEnvironment — NS_ENUM(NSInteger) — Production=0, Sandbox=1, Debug=2
2. MyLogLevel — NS_ENUM(NSInteger) — None=0, Error=1, Warning=2, Info=3, Debug=4
3. MyFeatureFlags — NS_OPTIONS(NSUInteger) — None=0, FeatureA=1, FeatureB=2, FeatureC=4
4. MyErrorCode — NS_ENUM(NSInteger) — Unknown=0, Network=1, Auth=2, NotFound=3

### Constants (6)
1. MyErrorDomain — NSString constant
2. MyDidConfigureNotification — NSNotificationName
3. MyVersionString — NSString constant
4. MyVersionNumber — double constant

### Blocks (2)
1. MyCompletionHandler — void(^)(MyResult *_Nullable, NSError *_Nullable)
2. MyProgressHandler — void(^)(double progress)
```

## What to Skip

Not everything in headers needs binding. Skip:

- **Internal/private prefixed APIs** — Methods starting with `_` or containing `internal`/`private`
- **Deprecated-only APIs** — If already deprecated and there's a replacement, consider skipping
- **C functions** — Unless essential; they need `[DllImport]` P/Invoke instead of binding attributes
- **Inline functions / macros** — Cannot be bound; reimplement in C# if needed
- **Unavailable APIs** — Marked with `NS_UNAVAILABLE` or `UNAVAILABLE_ATTRIBUTE`
- **Swift-only APIs** — Not marked `@objc`, won't appear in `-Swift.h`
- **Test/debug-only classes** — Internal testing utilities

## References

- **[references/objc-declaration-patterns.md](references/objc-declaration-patterns.md)** — Complete reference of ObjC declaration syntax patterns and their C# binding equivalents

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dalexsoto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
