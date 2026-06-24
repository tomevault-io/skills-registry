---
name: first-plan-lens-mobile
description: Stack lens para mobile - React Native, Swift (iOS), Kotlin (Android), Flutter. Use durante Discovery quando pubspec.yaml, Package.swift, build.gradle.kts (Android) ou react-native em deps for detectado. Cobre navegação, estado, módulos nativos, build/distribuição. Use when this capability is needed.
metadata:
  author: vynazevedo
---

# Lens Mobile

## Detecção

| Sinal | Variante |
|-------|----------|
| `pubspec.yaml` + `lib/main.dart` | Flutter |
| `Package.swift` + `Sources/` | Swift Package |
| `*.xcodeproj/` ou `*.xcworkspace/` | iOS native (Swift/ObjC) |
| `AndroidManifest.xml` + `build.gradle*` | Android nativo |
| `react-native` em deps + `android/` + `ios/` | React Native bare |
| `expo` em deps + `app.json`/`app.config.js` | Expo |

## Áreas de extração

### Navegação

React Native:
- `react-navigation` (stack, tab, drawer)
- `expo-router` (file-based)

Flutter:
- `Navigator` 1.0 ou 2.0
- `go_router`, `auto_route`

iOS Swift:
- `UINavigationController` / SwiftUI `NavigationStack`

Android Kotlin:
- Jetpack Navigation Component
- Compose Navigation

### Estado

React Native:
- Mesmas opções de TS (`zustand`, `redux`, `context`, `mobx`)
- `react-query` / `swr` para server state
- AsyncStorage / MMKV (persistência local)

Flutter:
- `provider`, `riverpod`, `bloc`, `getx`
- `shared_preferences`, `hive`, `isar` (persistência local)

iOS:
- ObservableObject + Combine
- TCA (The Composable Architecture)
- Core Data / SwiftData

Android:
- ViewModel + LiveData / StateFlow
- Room (DB)
- DataStore

### Módulos nativos / bridges

- React Native: `expo-modules`, custom native modules
- Flutter: `MethodChannel`, plugins
- Bridges com codegen (`react-native-codegen`)

### Build e distribuição

- Expo: EAS Build, EAS Submit
- React Native bare: Fastlane, manual Xcode/Gradle
- Flutter: `flutter build ipa/apk`, Codemagic, Bitrise
- iOS native: Fastlane, Xcode Cloud
- Android native: Fastlane, Gradle release config, Play Console upload

### Variantes / flavors

- Android: build flavors em build.gradle (e.g., dev/staging/prod)
- iOS: Schemes + xcconfig
- Flutter: flavors via `--flavor`
- RN: `app.json` + plugins de variant

### Permissões / privacidade

- iOS: `Info.plist` keys (NSLocationWhenInUseUsageDescription, etc)
- Android: `<uses-permission>` em manifest
- Privacy manifest (iOS 17+)

### Push / deep links

- FCM, OneSignal, expo-notifications
- Universal Links (iOS) / App Links (Android)
- Deep link routing config

### Testing

- Unit tests por linguagem (Jest/Vitest, XCTest, JUnit, flutter_test)
- E2E: Detox (RN), Maestro (cross), XCUITest, Espresso, integration_test (Flutter)

### CI/CD mobile

- Build em CI? (GitHub Actions, Bitrise, Codemagic)
- Code signing automatizado?
- TestFlight / Internal Testing pipeline

## Output

Padrão. Atenção especial:
- `01-topology/deployments.md` - como construir e distribuir builds
- `02-conventions/security.md` - storage seguro (Keychain, Keystore), certificate pinning
- `03-reuse/components.md` - design system mobile específico

## Confidence rules

Aumentar:
- Estrutura clara separando UI / lógica / persistência
- Tipagem consistente (TS strict em RN, Swift sem `Any`, Kotlin sem !!)

Reduzir:
- Mistura de paradigmas (e.g., UIKit + SwiftUI sem strategy clara)
- Builds quebrados em alguma plataforma (CI vermelha em uma das stores)

## Anti-padrões comuns

- Lógica de negócio em componente UI
- API keys hardcoded em código (deveria estar em variables/Info.plist + obfuscation)
- Secret scanning ignorado
- AsyncStorage/SharedPreferences guardando dados sensíveis sem encryption

---
> Source: [vynazevedo/first-plan](https://github.com/vynazevedo/first-plan) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
