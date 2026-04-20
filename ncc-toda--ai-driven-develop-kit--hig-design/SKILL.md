---
name: hig-design
description: Apple Human Interface Guidelines (HIG) に基づく UI 設計・実装支援。SwiftUI/UIKit で新規 UI を設計する際に使用。「設定画面を作りたい」「リスト表示はどうすればいい」「ナビゲーション構造を検討したい」「どのコンポーネントが適切か」といった設計・実装の相談時にトリガー。 Use when this capability is needed.
metadata:
  author: ncc-toda
---

# HIG UI 設計支援

Apple Human Interface Guidelines に基づいて、Apple プラットフォーム向け UI の設計・実装を支援する。

## 設計ワークフロー

```text
1. プラットフォーム特定（iOS/iPadOS/macOS/watchOS/visionOS）
2. ナビゲーション構造決定
3. コンポーネント選択
4. アクセシビリティ考慮
5. SwiftUI コード提案
```

## ナビゲーション構造の決定

```text
iOS:
├─ 画面数少ない → NavigationStack
└─ 画面数多い → TabView (最大 5 タブ)

iPadOS:
├─ compact → TabView
└─ regular → NavigationSplitView

macOS:
└─ NavigationSplitView (2-3 列)

watchOS:
├─ リスト → NavigationStack + List
└─ ページ → TabView (PageTabViewStyle)
```

## コンポーネント選択クイックガイド

| 用途           | SwiftUI               |
| :------------- | :-------------------- |
| テキストリスト | `List`                |
| 画像グリッド   | `LazyVGrid`           |
| 階層ナビ       | `NavigationSplitView` |
| 設定画面       | `Form`                |
| グラフ         | `Chart` (iOS 16+)     |
| 共有           | `ShareLink`           |

## 必須アクセシビリティ

```swift
// 1. システムフォント（Dynamic Type 対応）
Text("見出し").font(.headline)

// 2. セマンティックカラー
Text("ラベル").foregroundStyle(.primary)

// 3. タップターゲット 44pt
Button("") { }.frame(minWidth: 44, minHeight: 44)

// 4. VoiceOver ラベル
Image(systemName: "star").accessibilityLabel("お気に入り")
```

## 参照ファイル

詳細が必要な場合に参照：

- **[component-selection.md](references/component-selection.md)**: コンポーネント選択の詳細ガイド
- **[layout-patterns.md](references/layout-patterns.md)**: レイアウトパターンとコード例
- **[platform-guide.md](references/platform-guide.md)**: プラットフォーム別設計ガイド
- **[accessibility.md](references/accessibility.md)**: アクセシビリティ実装詳細

## 基本テンプレート

### iOS アプリ

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            TabView {
                NavigationStack {
                    ContentView()
                }
                .tabItem { Label("ホーム", systemImage: "house") }

                SettingsView()
                    .tabItem { Label("設定", systemImage: "gear") }
            }
        }
    }
}
```

### iPad/Mac 対応アプリ

```swift
@Environment(\.horizontalSizeClass) var sizeClass

var body: some View {
    if sizeClass == .compact {
        TabView { /* iPhone/iPad compact */ }
    } else {
        NavigationSplitView {
            Sidebar()
        } detail: {
            DetailView()
        }
    }
}
```

### 設定画面

```swift
Form {
    Section("アカウント") {
        TextField("名前", text: $name)
        TextField("メール", text: $email)
            .textContentType(.emailAddress)
    }

    Section("通知") {
        Toggle("プッシュ通知", isOn: $pushEnabled)
    }
}
.navigationTitle("設定")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ncc-toda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
