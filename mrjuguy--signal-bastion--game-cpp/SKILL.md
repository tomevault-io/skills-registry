---
name: game-cpp
description: C++ Coding Standards for Game Development (Unreal Engine Context). Use when this capability is needed.
metadata:
  author: mrjuguy
---

# Game C++ Standards

## 1. Memory Management

- **Unreal Garbage Collection**:
  - Use `UPROPERTY()` on all `UObject*` member variables to prevent them from being garbage collected.
  - Use `TWeakObjectPtr<>` if you do not want to hold a hard reference.
- **Smart Pointers**:
  - For non-UObjects, use `TSharedPtr<>`, `TSharedRef<>`, `TUniquePtr<>`.
  - Avoid raw `new` / `delete` unless absolutely necessary (and wrapped immediately).

## 2. Unreal Header Tool (UHT)

- **Macros**: Ensure `GENERATED_BODY()` is at the top of the class.
- **Reflection**: Use `UPROPERTY()` on variables you want to expose to Blueprints or the Editor.
  - `EditAnywhere`, `BlueprintReadWrite`, `Category="Config"` are standard.
- **Functions**: Use `UFUNCTION()` to expose to Blueprints.
  - `BlueprintCallable`, `BlueprintPure`.

## 3. Performance

- **Ticking**:
  - Disable `PrimaryActorTick.bCanEverTick = false;` if the actor does not need to update every frame.
  - Use Timers (`GetWorld()->GetTimerManager()`) for periodic events instead of checking `DeltaTime` in Tick.
- **Casts**:
  - Avoid `Cast<T>(Obj)` in `Tick()`. Cache references in `BeginPlay()`.
- **Logs**:
  - Use `UE_LOG(LogTemp, Warning, TEXT("Message %s"), *StringVariable);` sparseley in hot loops.

## 4. Data Structures

- **TArray**: Standard dynamic array.
- **TMap**: Key-Value pairs.
- **TSet**: Unique elements.
- **FString** vs **FName** vs **FText**:
  - `FString`: Mutable, expensive. Manipulation.
  - `FName`: Immutable, fast comparison. IDs/Keys.
  - `FText`: Localization. UI display.

## 5. Code Style

- **Braces**: BSD/Allman style (brace on new line).
- **Const**: Use `const` correctness whenever possible.
- **Headers**: Include minimal headers in `.h`. Use `forward declarations` (`class AMyActor;`) to reduce compile times.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrjuguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
