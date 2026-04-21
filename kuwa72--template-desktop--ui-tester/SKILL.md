---
name: ui-tester
description: Use when working with a structured method for an AI agent to verify the graphical user interface and interactive behavior of the application.
metadata:
  author: kuwa72
---
# Skill: UI & Behavioral Tester

This skill provides a structured method for an AI agent to verify the graphical user interface and interactive behavior of the application.

## 📋 Verification Checklist
When performing UI verification, check the following:
- [ ] **Visual Consistency**: Colors, fonts, and spacing match `ui_ux.md`.
- [ ] **Responsiveness**: Layout handles window resizing without breaking.
- [ ] **Interactivity**: Buttons have hover/active states; clicks trigger expected actions.
- [ ] **Backend Integration**: Verify that actions requiring the Rust backend (e.g., file saving) provide correct feedback in the UI.
- [ ] **Error Handling**: Verify that error states (e.g., failed command) are displayed gracefully.

## 🛠️ Verification Procedure
1.  **Start Dev Server**: Run `npm run tauri dev`.
2.  **Open Browser**: Use `open_browser_url` to access the local dev server (usually `http://localhost:1420`).
3.  **Interact**: Use `browser_click`, `browser_type`, and `browser_press_key` to simulate user actions.
4.  **Capture Proof**: Use the recording or screenshot features of the browser tool to demonstrate successful verification.
5.  **Report**: Summarize findings in the `walkthrough.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kuwa72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
