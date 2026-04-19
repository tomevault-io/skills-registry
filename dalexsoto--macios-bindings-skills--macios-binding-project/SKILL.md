---
name: macios-binding-project
description: Create standalone .NET binding library projects for third-party native iOS/macOS xcframeworks. Use when creating a new C# binding project for a vendor xcframework (e.g., Facebook SDK, Firebase, custom frameworks), scaffolding the .csproj and binding files, extracting API definitions from Objective-C headers, or setting up NuGet packaging for native library bindings. Covers project structure, header analysis, ApiDefinition.cs generation, and build/packaging. Use when this capability is needed.
metadata:
  author: dalexsoto
---

# .NET macOS/iOS Binding Project

Create standalone .NET binding library projects for third-party native xcframeworks.

## When to Use

- Binding a third-party xcframework (vendor SDK) for use in .NET MAUI / .NET iOS apps
- Creating a NuGet package wrapping a native iOS/macOS library
- NOT for binding Apple platform frameworks in the dotnet/macios repo (use `macios-bindings` skill instead)

## Workflow

### 1. Analyze the xcframework

Run `scripts/analyze_xcframework.py` to extract framework metadata:

```bash
python3 scripts/analyze_xcframework.py path/to/MyFramework.xcframework
```

This outputs: supported platforms/architectures, available headers, module map info, and whether the framework uses Swift.

### 2. Scaffold the Binding Project

Run `scripts/scaffold_binding_project.sh` to create the project structure:

```bash
scripts/scaffold_binding_project.sh MyFramework path/to/MyFramework.xcframework [output-dir]
```

This creates:

```
MyFramework.Binding/
├── MyFramework.Binding.csproj
├── ApiDefinition.cs
├── StructsAndEnums.cs
└── native/
    └── (symlink or copy of xcframework)
```

### 3. Extract API Surface from Headers

Read the Objective-C headers in the xcframework's `Headers/` directory. For each public class, protocol, enum, and constant, create the corresponding C# binding.

For header analysis guidance and Objective-C to C# translation patterns, see [references/header-translation.md](references/header-translation.md).

For the complete .csproj configuration reference, see [references/project-configuration.md](references/project-configuration.md).

### 4. Write ApiDefinition.cs

Translate Objective-C headers into C# binding interfaces. Key rules:

```csharp
using Foundation;
using ObjCRuntime;
using UIKit;

namespace MyFramework {

    // For each @interface — create [BaseType] interface
    [BaseType (typeof (NSObject))]
    interface MyClass {
        [Export ("name")]
        string Name { get; set; }

        [Export ("performAction:")]
        void PerformAction (string action);
    }

    // For each @protocol — create [Protocol, Model] interface + empty I-interface
    [Protocol, Model]
    [BaseType (typeof (NSObject))]
    interface MyDelegate {
        [Abstract]
        [Export ("didComplete:")]
        void DidComplete (MyClass sender);
    }

    interface IMyDelegate {}
}
```

### 5. Write StructsAndEnums.cs

```csharp
using ObjCRuntime;

namespace MyFramework {
    [Native]
    public enum MyStatus : long {
        Unknown = 0,
        Ready = 1,
        Error = 2,
    }
}
```

### 6. Build and Test

```bash
dotnet build MyFramework.Binding/MyFramework.Binding.csproj
```

Fix any generator errors (CS*, BI* warnings). Common issues:
- Selector mismatch — verify against headers
- Missing `[NullAllowed]` — check nullability annotations
- Type conflicts — use `[BaseType (Name = "ObjCName")]` for naming overrides

### 7. Package as NuGet

```bash
dotnet pack MyFramework.Binding/MyFramework.Binding.csproj -c Release
```

## Critical Lessons (from real-world bindings)

These are common runtime and compile-time pitfalls discovered when binding real xcframeworks (e.g., Facebook SDK):

### Swift Classes Without Default Init

Many Swift classes crash at runtime with `Use of unimplemented initializer 'init()'` if you allow default construction. **Always add `[DisableDefaultCtor]`** on Swift classes unless you have confirmed `init()` works:

```csharp
[DisableDefaultCtor]
[BaseType (typeof (NSObject))]
interface SwiftClass {
    [Export ("initWithName:")]
    NativeHandle Constructor (string name);
}
```

### Swift Factory Class Methods Don't Exist at Runtime

Swift convenience initializers exposed as ObjC class methods (e.g., `+[FBSDKSharePhoto photoWithImage:isUserGenerated:]`) may not exist in the ObjC runtime even though they appear in the `-Swift.h` header. **Prefer binding the `initWith*` constructor** over `+classMethod` factory methods for Swift classes. Mark factory methods with `[return: NullAllowed]` if you bind them, and always test at runtime.

### Field Library Name Must Be Framework Name

When binding `[Field]` constants from an xcframework, use the **framework name** (not `"__Internal"`):

```csharp
// ✅ Correct — framework name
[Field ("FBSDKErrorDomain", "FBSDKCoreKit")]
NSString ErrorDomain { get; }

// ❌ Wrong — __Internal is for symbols in the main executable
[Field ("FBSDKErrorDomain", "__Internal")]
NSString ErrorDomain { get; }
```

### NSSet/NSDictionary Generic Constraints

`NSSet<T>` and `NSDictionary<TKey, TValue>` require `T` to implement `INativeObject`. Custom bound classes don't satisfy this. Use untyped `NSSet` / `NSDictionary` for custom types:

```csharp
// ❌ Won't compile — MyClass doesn't implement INativeObject
NSSet<MyClass> Items { get; }

// ✅ Use untyped
NSSet Items { get; }

// ✅ Built-in types work fine
NSSet<NSString> Tags { get; }
```

### Method Name Conflicts with NSObject

Methods named `Handle`, `Description`, `Hash`, `IsEqual` conflict with inherited `NSObject` members. **Rename** or use `[New]`:

```csharp
// Rename to avoid conflict with NSObject.Handle property
[Export ("handle:")]
void HandleUrl ([NullAllowed] NSUrl url);

// Or use [New] to explicitly hide
[New]
[Export ("description")]
string Description { get; }
```

### Multi-TFM Support (iOS + Mac Catalyst)

When xcframeworks include both `ios-arm64` and `ios-arm64_x86_64-maccatalyst` slices, target both:

```xml
<TargetFrameworks>net10.0-ios;net10.0-maccatalyst</TargetFrameworks>
```

The consuming app project needs its own `<NativeReference>` entries pointing to the xcframeworks — native references from binding project references don't automatically flow to the app at link time.

### Constructor Pattern

Use `NativeHandle` as the return type for constructors (not `IntPtr`):

```csharp
[Export ("initWithFrame:")]
NativeHandle Constructor (CGRect frame);

// Failable init — return NullAllowed
[return: NullAllowed]
[Export ("initWithString:")]
NativeHandle Constructor (string value);
```

## References

- **[references/header-translation.md](references/header-translation.md)** — Translating Objective-C headers to C# bindings: classes, protocols, properties, methods, blocks, enums, constants
- **[references/project-configuration.md](references/project-configuration.md)** — .csproj configuration for binding projects: target frameworks, native references, NuGet metadata

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dalexsoto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
