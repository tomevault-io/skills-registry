---
name: maui-performance
description: > Use when this capability is needed.
metadata:
  author: davidortinau
---

# .NET MAUI Performance Optimization

## Profiling

- **dotnet-trace**: Cross-platform profiler for all MAUI targets. Collect traces with:
  ```shell
  dotnet-trace collect --process-id <PID> --providers Microsoft-DotNETCore-SampleProfiler
  ```
- **PerfView** (Windows only): Use for deep allocation and CPU analysis on WinUI targets.
- Profile before optimizing — measure, don't guess.

## Compiled Bindings

- Always set `x:DataType` on pages/views to enable compiled bindings.
- Compiled bindings are **8–20× faster** than reflection-based bindings.
- **Required** for NativeAOT and full trimming — reflection bindings break under trimming.
- Apply `x:DataType` at the highest reasonable scope (page or root element).

```xml
<ContentPage xmlns:vm="clr-namespace:MyApp.ViewModels"
             x:DataType="vm:MainViewModel">
    <Label Text="{Binding Title}" />
</ContentPage>
```

- For `DataTemplate` inside `CollectionView`/`ListView`, set `x:DataType` on the template root:

```xml
<DataTemplate x:DataType="model:Item">
    <Label Text="{Binding Name}" />
</DataTemplate>
```

- **Minimize unnecessary bindings**: Static content that never changes doesn't need `{Binding}`. Use literal values or `x:Static` instead.

## Layout Optimization

- **Avoid nested single-child layouts** — a `StackLayout` with one child is pointless overhead.
- **Prefer `Grid` over nested `StackLayout`/`VerticalStackLayout`** — Grid resolves layout in a single pass.
- Use `IsVisible="False"` to hide elements rather than adding/removing from the tree at runtime.
- Remove unnecessary wrapper containers — every layout adds a measure/arrange pass.
- **Avoid `AbsoluteLayout`** when `Grid` with row/column definitions achieves the same result.
- Set explicit `WidthRequest`/`HeightRequest` where sizes are known to reduce measure passes.

## CollectionView and ListView

- Use `ItemSizingStrategy="MeasureFirstItem"` on `CollectionView` when items have uniform height — avoids measuring every item individually.

```xml
<CollectionView ItemSizingStrategy="MeasureFirstItem"
                ItemsSource="{Binding Items}">
```

- Prefer `CollectionView` over `ListView` for new development.
- Keep `DataTemplate` visual trees shallow — fewer nested layouts per item.

## Image Optimization

- **Size images to display resolution** — don't load a 4000×3000 image for a 200×200 display.
- Use `Aspect="AspectFill"` or `AspectFit` and set explicit dimensions.
- **Cache downloaded images**: `CachingEnabled="True"` (default) on `UriImageSource`, set `CacheValidity`.
- Use platform-appropriate formats (WebP for Android, HEIF/PNG for iOS).
- For icons and simple graphics, prefer SVG or vector-based font icons.

## Resource Dictionaries

- **App-level** (`App.xaml`): Shared styles, colors, converters used across multiple pages.
- **Page-level**: Styles/resources specific to a single page — keeps App.xaml lean.
- Avoid duplicating resources across dictionaries.
- Use `MergedDictionaries` to organize large resource sets without bloating a single file.

## Startup Optimization

- Minimize work in `App` constructor and `MainPage` constructor.
- Defer non-essential service registration — use lazy initialization where possible.
- Reduce the number of XAML pages parsed at startup (use `Shell` routing for deferred loading).
- Remove unused `Handlers`/`Effects` registrations from `MauiProgram.cs`.
- On Android, ensure `AndroidManifest.xml` doesn't force unnecessary permissions checks at launch.

## Trimming

Enable IL trimming to reduce app size and remove unused code:

```xml
<PropertyGroup>
    <PublishTrimmed>true</PublishTrimmed>
    <TrimMode>full</TrimMode>
</PropertyGroup>
```

- Test thoroughly — trimming can remove code accessed only via reflection.
- Use `[DynamicDependency]` or `[UnconditionalSuppressMessage]` to preserve reflection targets.
- Compiled bindings are essential — reflection-based bindings will be trimmed away.

## NativeAOT

- NativeAOT compiles to native code ahead-of-time for faster startup and smaller footprint.
- **Requirements**: compiled bindings, no unconstrained reflection, trimming-compatible code.

```xml
<PropertyGroup>
    <PublishAot>true</PublishAot>
    <PublishTrimmed>true</PublishTrimmed>
</PropertyGroup>
```

- Audit third-party NuGet packages for trimming/AOT compatibility.
- Use `[JsonSerializable]` source generators instead of reflection-based JSON serialization.
- Run `dotnet publish` with AOT to verify no warnings before shipping.

## Quick Checklist

1. ✅ `x:DataType` on all pages, templates, and data-bound views
2. ✅ No nested single-child layouts
3. ✅ Grid preferred over nested StackLayouts
4. ✅ Images sized to display, caching enabled
5. ✅ `ItemSizingStrategy="MeasureFirstItem"` on uniform CollectionViews
6. ✅ Startup work minimized and deferred
7. ✅ Trimming enabled and tested
8. ✅ Profile with dotnet-trace before and after changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidortinau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
