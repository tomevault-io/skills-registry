---
name: windows-desktop-toggle-taskbar-hide
description: Toggles the "Automatically hide the taskbar in desktop mode" setting on Windows. Use this skill when the user wants to hide or show the taskbar automatically. Use when this capability is needed.
metadata:
  author: michaelkc
---
To enable auto-hiding the Windows taskbar:
pwsh -NoProfile -File .agent\skills\windows-desktop-toggle-taskbar-hide\enable-auto-hide.ps1

To disable auto-hiding the Windows taskbar:
pwsh -NoProfile -File .agent\skills\windows-desktop-toggle-taskbar-hide\disable-auto-hide.ps1

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michaelkc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
