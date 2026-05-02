---
name: burst-compatibility-auditor
description: Kiểm tra và sửa lỗi vi phạm quy tắc Burst Compiler trong code C# Unity. Dùng khi user yêu cầu kiểm tra Burst compatibility, sửa lỗi Burst, tối ưu code cho Burst, hoặc debug compilation errors liên quan đến Burst. Use when this capability is needed.
metadata:
  author: nukial
---

# Burst Compatibility Auditor

## Critical Instructions

- **Audit Before Ship**: Mọi code trong `Core/Jobs/`, `Core/Systems/` PHẢI được audit trước khi coi là hoàn thành.
- **Zero Tolerance**: Code vi phạm Burst = code sẽ fallback về Mono = mất toàn bộ hiệu suất. Không chấp nhận.
- **Fix, Not Just Report**: Khi phát hiện vi phạm, ĐỀ XUẤT cách sửa cụ thể, không chỉ báo lỗi.

## Burst Restriction Rules

### RULE 1: No Managed Types
Burst chỉ chấp nhận **unmanaged types** (blittable). KHÔNG dùng:

| ❌ CẤM | ✅ THAY THẾ |
|---------|-------------|
| `string` | `FixedString64Bytes`, `FixedString128Bytes` |
| `class` | `struct` |
| `object` | generic constraint `where T : unmanaged` |
| `List<T>` | `NativeList<T>` |
| `Dictionary<K,V>` | `NativeParallelHashMap<K,V>` |
| `T[]` (managed array) | `NativeArray<T>` |
| `delegate`, `Action`, `Func` | Function pointers (`FunctionPointer<T>`) |
| `interface` reference | Direct struct call hoặc `FunctionPointer` |
| `Nullable<T>` | Custom struct with `bool HasValue` |
| `enum` with non-int base | `enum : int` or `enum : byte` |

### RULE 2: No Try-Catch-Finally
```csharp
// ❌ CẤM
try { DoSomething(); } catch { }

// ✅ THAY THẾ: Check trước, không catch
if (IsValid(data)) { DoSomething(data); }
```

### RULE 3: No Boxing
```csharp
// ❌ CẤM: Boxing value type thành object
object o = myInt;
IComparable c = myFloat;

// ✅ THAY THẾ: Dùng generic constraint hoặc trực tiếp
void Process<T>(T value) where T : unmanaged { }
```

### RULE 4: No Static Mutable State
```csharp
// ❌ CẤM
static int Counter = 0;
void Execute() { Counter++; } // Race condition + Burst error

// ✅ THAY THẾ: Pass vào job field
public struct MyJob : IJobParallelFor
{
    public NativeArray<int> Counter; // Atomic hoặc per-thread
}
```

### RULE 5: No Virtual/Abstract Calls
```csharp
// ❌ CẤM: Virtual dispatch
class Base { public virtual void Do() {} }
class Derived : Base { public override void Do() {} }

// ✅ THAY THẾ: Generic struct
struct ConcreteProcessor : IProcessor { public void Do() {} }
```

### RULE 6: No Allocation in Execute
```csharp
// ❌ CẤM
void Execute(int i) {
    var temp = new NativeArray<float>(10, Allocator.Temp); // GC in hot path!
}

// ✅ THAY THẾ: Allocate trước, pass vào job
public NativeArray<float> TempBuffer; // Allocated in system, per-thread
```

### RULE 7: No UnityEngine API
```csharp
// ❌ CẤM (trong Burst context)
UnityEngine.Debug.Log("msg");
UnityEngine.Mathf.PerlinNoise(x, y);
UnityEngine.Random.Range(0, 1);

// ✅ THAY THẾ
Unity.Mathematics.noise.cnoise(new float2(x, y));
Unity.Mathematics.Random random = new Unity.Mathematics.Random(seed);
```

### RULE 8: No String Operations
```csharp
// ❌ CẤM
string result = "Hello" + " World";
string.Format("{0}", value);

// ✅ THAY THẾ (nếu thực sự cần string trong Burst)
FixedString64Bytes fs = "Hello";
fs.Append(" World");
```

### RULE 9: No LINQ
```csharp
// ❌ CẤM
var filtered = array.Where(x => x > 0).ToArray();
var sum = array.Sum();

// ✅ THAY THẾ: Manual loop
float sum = 0;
for (int i = 0; i < array.Length; i++)
    sum += array[i];
```

### RULE 10: No Recursion (Deep)
```csharp
// ❌ NGUY HIỂM: Stack overflow risk, Burst có thể từ chối
void Recurse(int depth) { if (depth > 0) Recurse(depth - 1); }

// ✅ THAY THẾ: Iterative với explicit stack
var stack = new NativeList<int>(Allocator.Temp);
stack.Add(startValue);
while (stack.Length > 0) {
    var current = stack[stack.Length - 1];
    stack.RemoveAt(stack.Length - 1);
    // Process...
}
```

## Audit Workflow

### Step 1: Scan File
Tìm các pattern vi phạm trong file `.cs`:

```
Scan targets:
├── Structs with [BurstCompile]
├── Structs implementing IJob* interfaces
├── Structs implementing ISystem
├── Static methods called from Burst context
└── Utility structs used as job fields
```

### Step 2: Check Patterns
Cho mỗi file, kiểm tra danh sách vi phạm:

```markdown
## Audit Report: {FileName}.cs

### Violations Found:
| Line | Rule | Severity | Issue | Fix |
|------|------|----------|-------|-----|
| 42 | RULE 1 | ERROR | `string name` field in job | Replace with `FixedString64Bytes` |
| 78 | RULE 6 | ERROR | `new NativeArray` in Execute | Pre-allocate in system |
| 103 | RULE 7 | WARN | `Debug.Log` in Burst method | Remove or guard with `#if !UNITY_BURST_COMPILED` |

### Clean Passes:
- [x] No managed types in struct fields
- [x] No try-catch blocks
- [ ] No static mutable access ← VIOLATION at line 42
```

### Step 3: Auto-Fix Suggestions
Với mỗi violation, đề xuất code thay thế cụ thể:

```csharp
// LINE 42 - BEFORE (violation):
public string Name;

// LINE 42 - AFTER (fixed):
public FixedString64Bytes Name;
```

## Common False Positives

Các trường hợp KHÔNG phải vi phạm (đừng report):

1. **Managed code ngoài Burst context**: Code trong `MonoBehaviour`, Editor script, v.v.
2. **`Allocator.Temp` trong `IJob.Execute()`** (single-threaded job): Cho phép, nhưng tránh trong `IJobParallelFor`.
3. **`SharedStatic<T>`**: Static nhưng Burst-safe. Đây là pattern hợp lệ.
4. **`[BurstDiscard]` methods**: Chủ ý non-Burst fallback, hợp lệ.
5. **`#if !UNITY_BURST_COMPILED` guards**: Debug code có guard, hợp lệ.

## Integration with Unity Console

Sau khi audit code, kiểm tra Unity Console cho:

```
// Burst compilation errors xuất hiện như:
// (0,0): Burst error BC1016: The managed type `System.String` is not supported...
// (0,0): Burst error BC1045: Accessing managed method `Debug.Log` is not supported...

// Burst compilation warnings:
// Burst warning BC1370: An exception was thrown during the compilation of...
```

## Validation Checklist

- [ ] Không có managed type nào trong struct fields của Job/System
- [ ] Không có try-catch-finally trong Burst context
- [ ] Không có boxing (value type → object/interface)
- [ ] Không có static mutable state access
- [ ] Không có virtual/abstract calls
- [ ] Không có allocation trong Execute()
- [ ] Không có UnityEngine API trong Burst context
- [ ] Không có string operations (ngoài FixedString)
- [ ] Không có LINQ
- [ ] Không có deep recursion
- [ ] `[BurstCompile]` có mặt trên mọi Job struct VÀ ISystem
- [ ] Unity Console sạch Burst errors sau compilation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nukial) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
