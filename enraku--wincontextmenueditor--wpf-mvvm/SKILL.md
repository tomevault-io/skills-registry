---
name: wpf-mvvm
description: WPF MVVM pattern guide for this project. Use when adding new views, view models, commands, data bindings, converters, or UI components. Triggers on: creating new UserControls or Windows, adding data bindings, implementing ICommand, creating value converters, or working with ResourceDictionary/styles. Use when this capability is needed.
metadata:
  author: enraku
---

# WPF MVVM Patterns

## Architecture

This project uses MVVM without a framework. Key base classes:

- **ViewModelBase** (`ViewModels/ViewModelBase.cs`): `INotifyPropertyChanged` with `SetProperty<T>` helper
- **RelayCommand** (`ViewModels/RelayCommand.cs`): `ICommand` implementation with `Action`/`Func` constructors

## Adding a New View + ViewModel

1. Create ViewModel in `ViewModels/`:
```csharp
namespace WinContextMenuEditor.ViewModels;

public class FooViewModel : ViewModelBase
{
    private string _bar = string.Empty;
    public string Bar
    {
        get => _bar;
        set => SetProperty(ref _bar, value);
    }

    public ICommand DoSomethingCommand { get; }

    public FooViewModel()
    {
        DoSomethingCommand = new RelayCommand(OnDoSomething);
    }

    private void OnDoSomething() { /* ... */ }
}
```

2. Create View XAML in `Views/`:
```xml
<UserControl x:Class="WinContextMenuEditor.Views.FooView"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:vm="clr-namespace:WinContextMenuEditor.ViewModels">
    <UserControl.DataContext>
        <vm:FooViewModel/>
    </UserControl.DataContext>
    <!-- Use {Binding PropertyName} for data binding -->
</UserControl>
```

## App-Level Resources

Registered in `App.xaml`:
- `{StaticResource BoolToVis}` - `BoolToVisibilityConverter` (supports `ConverterParameter=Invert`)
- `{StaticResource InvertBoolConverter}` - inverts bool values
- Styles: `ActionButton`, `ToolbarButton`, `FormLabel`, `FormTextBox`, `SearchTextBox`, `CategoryListBox`, `StatusBar`
- Strings: `{DynamicResource KeyName}` for i18n (see `Resources/Strings.*.xaml`)

## i18n

- Add entries to both `Resources/Strings.en-US.xaml` and `Resources/Strings.ja-JP.xaml`
- Use `{DynamicResource StringKey}` in XAML (not StaticResource - dynamic enables runtime switching)

## Dialog Pattern

For modal dialogs (see `AddEditDialog` as example):
1. Create Window with `DataContext` bound to dialog ViewModel
2. Set `DialogResult` in code-behind OK/Cancel click handlers
3. Show with `dialog.ShowDialog() == true`

## Conventions

- Properties: use `SetProperty(ref _field, value)` for change notification
- Commands: `RelayCommand` with `Action` or `Action<object?>` + optional `Func<bool>` canExecute
- Converters: register in `App.xaml` ResourceDictionary
- Keep code-behind minimal - logic in ViewModels, UI wiring only in code-behind

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enraku) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
