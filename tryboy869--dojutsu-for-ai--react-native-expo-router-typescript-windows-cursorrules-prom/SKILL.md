---
name: react-native-expo-router-typescript-windows-cursorrules-prom
description: Apply for react-native-expo-router-typescript-windows-cursorrules-prom. --- description: Provides specific version compatibility notes for NativeWind and Tailwind CSS to prevent common installation errors. globs: package.json Use when this capability is needed.
metadata:
  author: Tryboy869
---

# react-native-expo-router-typescript-windows-cursorrules-prompt-file

---
description: Provides specific version compatibility notes for NativeWind and Tailwind CSS to prevent common installation errors.
globs: package.json
---
- NativeWind and Tailwind CSS compatibility:
  - Use nativewind@2.0.11 with tailwindcss@3.3.2.
  - Higher versions may cause 'process(css).then(cb)' errors.
  - If errors occur, remove both packages and reinstall specific versions:
    npm remove nativewind tailwindcss
    npm install nativewind@2.0.11 tailwindcss@3.3.2

---
> Source: [Tryboy869/dojutsu-for-ai](https://github.com/Tryboy869/dojutsu-for-ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
