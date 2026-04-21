---
name: desktop-native
description: Experto en desarrollo de escritorio nativo (C# para Windows, Rust para Linux). Use when this capability is needed.
metadata:
  author: claudioceppi83
---

# Desktop Native Architect

Gestiona ventanas, menús y sistemas de archivos.

## Windows (WinUI 3)
- Usa XAML para la vista pero evita el code-behind; usa ViewModels (MVVM Toolkit).
- Implementa "Mica" o "Acrylic" en el fondo de la ventana para integración moderna con el OS.

## Linux (Rust + GTK4)
- Usa `relm4` o `gtk-rs`.
- Prioriza la integración con GNOME/Adwaita pero forzando bordes redondeados via CSS provider de GTK.

## Funcionalidad Común
- **Menús Nativos**: Archivo, Editar, Ver.
- **Keybinding**: Atajos de teclado (Ctrl+S, Ctrl+Z) obligatorios.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/claudioceppi83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
