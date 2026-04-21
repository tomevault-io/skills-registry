---
name: swift-dev-toolkit
description: | Use when this capability is needed.
metadata:
  author: r1ca18
---

# Swift Dev Toolkit

Swift/iOS/macOS 開発の自動化ツールキット。

## エージェントの行動原則

1. **手順を説明するのではなく、実行する**
2. **Fastlane 未設定なら自動セットアップしてから進める**
3. **エラーは修正してリトライ**（Build Loop）
4. **API で不可能な操作のみ Chrome 自動化を使用**

## Sub-Skills

タスクに応じて適切なサブスキルを使う。

| Sub-Skill    | 呼び出し                      | 用途                                          |
| ------------ | ----------------------------- | --------------------------------------------- |
| **build**    | `/swift-dev-toolkit:build`    | ビルドエラー修正ループ                        |
| **fastlane** | `/swift-dev-toolkit:fastlane` | Fastlane設定, TestFlight, App Store           |
| **project**  | `/swift-dev-toolkit:project`  | Bundle ID, App Groups, Widget, 翻訳, スクショ |
| **profile**  | `/swift-dev-toolkit:profile`  | パフォーマンス計測 (xctrace)                  |
| **xcodegen** | `/swift-dev-toolkit:xcodegen` | XcodeGen プロジェクト初期化                   |
| **docc**     | `/swift-dev-toolkit:docc`     | DocC チュートリアル                           |

## ルーティング

ユーザーの要求に応じて自動で適切なサブスキルに振り分ける:

- ビルドエラー、コンパイルエラー → **build**
- Fastlane、TestFlight、App Store、デプロイ → **fastlane**
- Bundle ID、App Groups、Widget、翻訳、スクショ → **project**
- パフォーマンス、プロファイル、ボトルネック、Instruments → **profile**
- XcodeGen、xcodeproj、新規プロジェクト → **xcodegen**
- DocC、チュートリアル → **docc**

## ファイル構成

```
swift-dev-toolkit/
├── SKILL.md                       # ルーター（このファイル）
├── skills/
│   ├── build/SKILL.md             # Build Loop
│   ├── fastlane/SKILL.md          # Fastlane + TestFlight + Deploy
│   ├── project/SKILL.md           # Project Setup + Widget + i18n + Screenshot
│   ├── profile/SKILL.md           # Performance Profiling (xctrace)
│   ├── xcodegen/SKILL.md          # XcodeGen
│   └── docc/SKILL.md              # DocC
├── scripts/
│   ├── build_loop.sh
│   └── parse_profile.py           # xctrace XML parser
├── templates/
│   ├── Fastfile
│   ├── Appfile
│   ├── SharedDataManager.swift
│   ├── ExportOptions.plist
│   └── project.yml
└── references/
    ├── api_key_setup.md
    ├── fastlane_actions.md
    ├── docc_tutorial.md
    ├── troubleshooting.md
    └── xcodegen_guide.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/r1ca18) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
