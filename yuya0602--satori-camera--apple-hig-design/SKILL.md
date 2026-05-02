---
name: apple-hig-design
description: Apple の Human Interface Guidelines に準拠したデザインを作成します。iOS、iPadOS、macOS、watchOS、tvOS のデザインシステム、コンポーネント、レイアウト、タイポグラフィ、カラー、アイコン設計を行う際に使用します。UI/UX デザイン、アプリ設計、プロトタイプ作成時に最適です。 Use when this capability is needed.
metadata:
  author: yuya0602
---

# Apple Human Interface Guidelines デザインスキル

Apple の Human Interface Guidelines (HIG) に準拠した、洗練されたユーザーインターフェースデザインを作成するためのスキルです。

## このスキルを使用するタイミング

以下のような場合に使用してください:
- iOS、iPadOS、macOS、watchOS、tvOS アプリのデザインを作成する時
- Apple プラットフォーム向けの UI コンポーネントを実装する時
- HIG に準拠したカラースキーム、タイポグラフィ、レイアウトを設計する時
- Apple デザイン原則に基づいたアイコンやグラフィックを作成する時
- アクセシビリティ対応のデザインを実装する時
- SF Symbols を活用したデザインを行う時

## Apple HIG の核心原則

### 1. **明瞭性 (Clarity)**
- テキストはすべてのサイズで読みやすく
- アイコンは正確で分かりやすく
- 装飾は適切で目的がある
- 機能が形を導く

### 2. **敬意 (Deference)**
- UIはコンテンツを理解する手助けをする
- コンテンツが画面全体を占める
- 半透明やぼかしがコンテキストのヒントを提供

### 3. **奥行き (Depth)**
- レイヤーと動きが階層を伝える
- タッチとフィードバックが理解を深める
- 移行によりナビゲーションが明確になる

## プラットフォーム別ガイドライン

### iOS / iPadOS

#### レイアウト
- **セーフエリア**: ノッチやホームインジケーターを避ける
- **マージン**: 最小 16pt (レギュラーサイズクラス)、20pt (ラージサイズクラス)
- **スペーシング**: 要素間は 8pt、16pt、24pt の倍数を使用
- **グリッド**: 8pt グリッドシステムを基本とする

#### タイポグラフィ
- **Dynamic Type**: ユーザーの設定に応じてテキストサイズを調整
- **フォント**: San Francisco (SF Pro、SF Compact、SF Mono)
- **テキストスタイル**:
  - Large Title: 34pt (Bold)
  - Title 1: 28pt (Regular)
  - Title 2: 22pt (Regular)
  - Title 3: 20pt (Regular)
  - Headline: 17pt (Semibold)
  - Body: 17pt (Regular)
  - Callout: 16pt (Regular)
  - Subheadline: 15pt (Regular)
  - Footnote: 13pt (Regular)
  - Caption 1: 12pt (Regular)
  - Caption 2: 11pt (Regular)

#### カラー
- **システムカラー**: `.systemBlue`、`.systemGreen` などを優先
- **セマンティックカラー**:
  - Label: プライマリテキスト
  - Secondary Label: セカンダリテキスト
  - Tertiary Label: 補助テキスト
  - System Background: 背景
  - Secondary System Background: グループ背景
- **ダークモード対応**: すべてのカラーはライト/ダーク両方で適切に表示

#### コンポーネント

**Navigation Bar**
- 高さ: 44pt (コンパクト)、96pt (ラージタイトル)
- タイトル位置: 中央または左揃え (ラージタイトル)
- ボタン: 最大2つまで (左右各1つ推奨)

**Tab Bar**
- 高さ: 49pt (iPhone)、50pt (iPad)
- タブ数: 2〜5個 (推奨は3〜4個)
- アイコンサイズ: 25x25pt または 24x24pt

**Buttons**
- 最小タップ領域: 44x44pt
- スタイル: Filled、Tinted、Gray、Plain
- 角丸: 標準 8pt、大きいボタン 12pt

**Lists**
- 行の高さ: 最小 44pt
- アイコン: 28x28pt または 29x29pt
- セパレーター: Inset または Full Width

**Cards**
- 角丸: 12pt または 16pt
- シャドウ: 微妙な影 (opacity 0.1-0.15)
- パディング: 16pt

### macOS

#### ウィンドウ
- **最小サイズ**: 800x600pt
- **ツールバー高さ**: 52pt (標準)、76pt (ラージ)
- **サイドバー幅**: 最小 150pt、推奨 200-250pt

#### コントロール
- **ボタン高さ**: 22pt (小)、28pt (標準)、32pt (大)
- **テキストフィールド高さ**: 21pt (小)、28pt (標準)

### watchOS

#### レイアウト
- **マージン**: 4-8pt
- **スタック間隔**: 4pt
- **グループパディング**: 8pt

#### タイポグラフィ
- SF Compact を使用
- 最小サイズ: 14pt

## アイコンとグラフィックス

### SF Symbols
- **サイズ**: Small (13pt)、Medium (17pt)、Large (22pt)
- **ウェイト**: Ultralight から Black まで9段階
- **レンダリングモード**:
  - Monochrome: 単色
  - Hierarchical: 階層的な透明度
  - Palette: 複数色
  - Multicolor: フルカラー

### アプリアイコン
- **iOS**: 1024x1024px (App Store)、60x60pt (ホーム画面 @3x)
- **角丸**: システムが自動適用 (手動で角丸不要)
- **デザイン原則**:
  - シンプルで認識しやすい
  - 一貫性のあるビジュアルスタイル
  - 細部まで配慮
  - テキストは避ける

## アクセシビリティ

### 色のコントラスト
- **標準テキスト**: 最小 4.5:1
- **大きいテキスト**: 最小 3:1
- **色だけに依存しない**: 情報を伝える際は形状やテキストも併用

### VoiceOver 対応
- すべての UI 要素にアクセシビリティラベルを提供
- カスタムコントロールに適切な trait を設定
- 意味のある順序で要素を配置

### Dynamic Type
- すべてのテキストで Dynamic Type をサポート
- レイアウトはテキストサイズの変更に対応

## インタラクションとアニメーション

### タップとジェスチャー
- **最小タップターゲット**: 44x44pt (iOS)、28x28pt (macOS)
- **スワイプ**: リストアイテムのアクション表示
- **ロングプレス**: コンテキストメニュー表示

### アニメーション
- **デュレーション**: 0.2-0.4秒 (標準)、0.5-0.8秒 (大きな変化)
- **イージング**:
  - ease-in-out (標準)
  - spring (自然な動き)
  - linear (進捗表示)
- **原則**: 意味がありパフォーマンスを損なわない

## デザインパターン

### オンボーディング
```markdown
1. 価値を明確に示す
2. 必要最小限の情報を求める
3. スキップ可能にする
4. インタラクティブなチュートリアル
```

### フォーム入力
```markdown
1. ラベルは常に表示
2. プレースホルダーは例示に使用
3. インラインバリデーション
4. エラーは具体的に説明
```

### 検索
```markdown
1. 検索バーは目立つ位置に
2. サジェストと最近の検索を表示
3. リアルタイムフィルタリング
4. 結果のないケースを処理
```

## AI アシスタント向け指示

このスキルが起動された場合、以下の手順に従ってください:

1. **プラットフォームの特定**: まず対象プラットフォーム (iOS/macOS/watchOS など) を確認
2. **デザイン要件の理解**: ユーザーが作成したいコンポーネントや画面を把握
3. **HIG 準拠の確認**: 以下をチェック
   - 適切なスペーシングとマージン
   - システムフォントとテキストスタイルの使用
   - システムカラーの活用
   - 最小タップ領域の確保
   - ダークモード対応
   - アクセシビリティ考慮
4. **コード生成時**:
   - SwiftUI または UIKit で実装
   - SF Symbols を優先的に使用
   - ネイティブコンポーネントを活用
   - カスタムスタイルは HIG に準拠
5. **検証**: 生成したデザイン/コードが HIG に準拠しているか確認

### 常に実施すること
- システム標準のコンポーネントを優先
- セマンティックカラーを使用
- Dynamic Type をサポート
- ダークモード対応を実装
- アクセシビリティラベルを提供
- SF Symbols を活用

### 避けること
- カスタムフォントの過度な使用
- ハードコードされたカラー値
- 固定されたテキストサイズ
- 44pt 未満のタップターゲット
- 意味のないアニメーション
- システム標準から大きく逸脱したデザイン

## コード例

### SwiftUI での標準的なリスト実装

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        NavigationStack {
            List {
                ForEach(items) { item in
                    HStack(spacing: 12) {
                        Image(systemName: item.icon)
                            .font(.title2)
                            .foregroundStyle(.blue)
                            .frame(width: 28, height: 28)

                        VStack(alignment: .leading, spacing: 4) {
                            Text(item.title)
                                .font(.headline)
                            Text(item.subtitle)
                                .font(.subheadline)
                                .foregroundStyle(.secondary)
                        }
                    }
                    .padding(.vertical, 8)
                }
            }
            .navigationTitle("アイテム")
            .navigationBarTitleDisplayMode(.large)
        }
    }
}
```

### HIG 準拠のボタンスタイル

```swift
struct PrimaryButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.headline)
                .foregroundStyle(.white)
                .frame(maxWidth: .infinity)
                .frame(height: 50)
                .background(.blue)
                .clipShape(RoundedRectangle(cornerRadius: 12))
        }
        .padding(.horizontal, 16)
    }
}
```

### カラースキームの実装

```swift
extension Color {
    static let appBackground = Color(.systemBackground)
    static let appSecondaryBackground = Color(.secondarySystemBackground)
    static let appLabel = Color(.label)
    static let appSecondaryLabel = Color(.secondaryLabel)
    static let appAccent = Color(.systemBlue)
}
```

### アクセシビリティ対応のカスタムコントロール

```swift
struct CustomControl: View {
    @State private var isSelected = false

    var body: some View {
        Button {
            isSelected.toggle()
        } label: {
            Image(systemName: isSelected ? "checkmark.circle.fill" : "circle")
                .font(.title2)
                .foregroundStyle(isSelected ? .blue : .secondary)
        }
        .frame(width: 44, height: 44) // 最小タップ領域確保
        .accessibilityLabel("選択")
        .accessibilityValue(isSelected ? "選択済み" : "未選択")
        .accessibilityAddTraits(isSelected ? [.isSelected] : [])
    }
}
```

## ベストプラクティス

### レイアウト
1. **8pt グリッドシステム**: すべてのスペーシングを 8 の倍数に
2. **セーフエリアの尊重**: 重要なコンテンツはセーフエリア内に配置
3. **レスポンシブ対応**: 異なる画面サイズ、向きに対応
4. **適切な余白**: 窮屈に感じさせない十分なスペース

### タイポグラフィ
1. **システムフォント優先**: SF Pro/Compact/Mono を使用
2. **Dynamic Type サポート**: テキストスタイルを使用
3. **読みやすさ**: 行間、文字間隔に配慮
4. **階層の明確化**: サイズとウェイトで情報の重要度を表現

### カラー
1. **セマンティックカラー**: システム定義の色を使用
2. **一貫性**: ブランドカラーは控えめに使用
3. **コントラスト**: 十分な色のコントラストを確保
4. **ダークモード**: 両モードで見やすい配色

### インタラクション
1. **即座のフィードバック**: タップ時に視覚的反応
2. **意図の明確化**: 何が起こるか予測可能に
3. **エラー防止**: 破壊的操作には確認ダイアログ
4. **一貫性**: プラットフォーム標準のジェスチャーを使用

## トラブルシューティング

**問題**: ダークモードでカラーが適切に表示されない
**解決策**: ハードコードされたカラー値を使用せず、システムカラーまたはアセットカタログで Light/Dark バリアントを定義

**問題**: Dynamic Type でレイアウトが崩れる
**解決策**: 固定サイズではなく、`.fixedSize()`、`.frame(minHeight:)`、`.lineLimit()` を適切に使用

**問題**: タップ領域が小さすぎる
**解決策**: 視覚的なサイズが小さくても、`.frame(width: 44, height: 44)` でタップ領域を確保

**問題**: VoiceOver で要素が正しく読み上げられない
**解決策**: `.accessibilityLabel()`、`.accessibilityValue()`、`.accessibilityHint()` を適切に設定

## 参考リソース

- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [SF Symbols](https://developer.apple.com/sf-symbols/)
- [Apple Design Resources](https://developer.apple.com/design/resources/)
- [WWDC Design Sessions](https://developer.apple.com/videos/design/)
- [Accessibility for iOS](https://developer.apple.com/accessibility/ios/)

## アップデート情報

このスキルは最新の HIG (iOS 17、macOS 14 以降) に基づいています。定期的に公式ドキュメントを確認し、最新のベストプラクティスに従ってください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yuya0602) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
