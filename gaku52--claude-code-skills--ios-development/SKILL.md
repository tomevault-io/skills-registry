---
name: ios-development
description: iOS開発のベストプラクティス、設計パターン、実装テクニック、よくあるトラブルと解決方法を包括的にカバー。MVVM、Clean Architecture、Combine、SwiftUI/UIKitの実践的なガイドを提供します。 Use when this capability is needed.
metadata:
  author: gaku52
---

# iOS Development Skill

## 📋 目次

1. [概要](#概要)
2. [いつ使うか](#いつ使うか)
3. [アーキテクチャパターン](#アーキテクチャパターン)
4. [UI開発](#ui開発)
5. [データ管理](#データ管理)
6. [ベストプラクティス](#ベストプラクティス)
7. [よくある問題](#よくある問題)
8. [Agent連携](#agent連携)

---

## 概要

このSkillは、iOS開発における全ての側面をカバーします：

- ✅ アーキテクチャパターン（MVVM, Clean Architecture, VIPER）
- ✅ SwiftUI/UIKit開発
- ✅ Reactive Programming（Combine, RxSwift）
- ✅ データ永続化（CoreData, Realm, UserDefaults）
- ✅ ネットワーク通信
- ✅ 非同期処理（async/await, DispatchQueue）
- ✅ Dependency Injection
- ✅ エラーハンドリング
- ✅ メモリ管理
- ✅ よくあるトラブルと解決方法

---

## 📚 公式ドキュメント・参考リソース

**このガイドで学べること**: アーキテクチャパターン、設計原則、実装テクニック
**公式で確認すべきこと**: 最新API、iOS 18の新機能、SwiftUI/UIKitリファレンス、App Store審査ガイドライン

### 主要な公式ドキュメント

- **[Apple Developer Documentation](https://developer.apple.com/documentation/)** - Apple公式ドキュメント
  - [SwiftUI](https://developer.apple.com/documentation/swiftui/) - SwiftUI完全リファレンス
  - [UIKit](https://developer.apple.com/documentation/uikit/) - UIKit完全リファレンス
  - [Swift](https://developer.apple.com/documentation/swift/) - Swift言語リファレンス
  - [Combine](https://developer.apple.com/documentation/combine/) - Reactive Programming
  - [Core Data](https://developer.apple.com/documentation/coredata/) - データ永続化

- **[Swift.org](https://www.swift.org/)** - Swift言語公式サイト
  - [The Swift Programming Language](https://docs.swift.org/swift-book/) - 完全ガイド
  - [Swift Evolution](https://apple.github.io/swift-evolution/) - 言語仕様の進化

- **[Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)** - UIデザインガイドライン
  - iOS、iPadOS、watchOS、macOSのデザイン原則
  - コンポーネント使用ガイド

- **[App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)** - 審査ガイドライン
  - 申請前の必読事項
  - リジェクト回避のポイント

### 関連リソース

- **[WWDC Videos](https://developer.apple.com/videos/)** - 年次カンファレンス動画
- **[Swift Forums](https://forums.swift.org/)** - Swift公式フォーラム
- **[Hacking with Swift](https://www.hackingwithswift.com/)** - チュートリアル集
- **[Ray Wenderlich](https://www.kodeco.com/)** - iOS開発教材
- **[Swift by Sundell](https://www.swiftbysundell.com/)** - 実践的なTips

---

## 学習の進め方

### 完全初心者向け：基礎から学ぶiOS開発

**対象者**：プログラミング初心者、iOS開発が初めての方

**学習時間**：約7〜9時間

iOS開発の基礎を体系的に学べる7つのガイドを用意しました。順番に学習することで、実践的なiOSアプリを構築できるようになります。

#### 📚 基礎ガイド（全7章）

1. **[iOS開発とは](./guides/basics/01-what-is-ios-development.md)** (30〜40分)
   - iOS開発の基本概念
   - iOSアプリの特徴
   - 開発に必要なもの
   - 技術スタック

2. **[Swift基礎](./guides/basics/02-swift-basics.md)** (1〜1.5時間)
   - Swift言語の基本文法
   - 変数、定数、データ型
   - 制御構文
   - 関数とオプショナル

3. **[Xcode入門](./guides/basics/03-xcode-intro.md)** (40〜50分)
   - Xcodeの基本操作
   - プロジェクトの作成方法
   - シミュレータの使い方
   - 便利なショートカット

4. **[SwiftUI基礎](./guides/basics/04-swiftui-basics.md)** (1〜1.5時間)
   - SwiftUIの基本概念
   - 基本的なViewコンポーネント
   - レイアウトの作成方法
   - 修飾子(Modifier)

5. **[レイアウトとUI](./guides/basics/05-view-layout.md)** (1〜1.5時間)
   - 高度なレイアウト技術
   - Spacer、Divider、ScrollView
   - カスタムコンポーネント
   - レスポンシブデザイン

6. **[状態管理](./guides/basics/06-state-management.md)** (1〜1.5時間)
   - @State による状態管理
   - @Binding による親子間のデータ共有
   - @StateObject / @ObservedObject
   - フォーム入力

7. **[初めてのアプリ作成](./guides/basics/07-first-app-tutorial.md)** (2〜3時間)
   - 総合演習：メモアプリ
   - MVVMアーキテクチャ
   - データの永続化
   - CRUD操作の実装

#### 🎯 学習の進め方

```
Week 1: 01→02→03（環境構築・Swift基礎）
Week 2: 04→05（SwiftUI・レイアウト）
Week 3: 06→07（状態管理・実践）
```

**学習後に作れるもの**：
- ✅ メモアプリ
- ✅ TODOリスト
- ✅ カウンターアプリ

---

## いつ使うか

### 自動的に参照されるケース

- 新しいiOSプロジェクトを開始する時
- 既存コードをリファクタリングする時
- アーキテクチャの選択・設計をする時
- 実装で迷った時
- エラーやクラッシュが発生した時

### 手動で参照すべきケース

- プロジェクトのアーキテクチャを決定する時
- 新しい機能の設計をする時
- パフォーマンス問題を調査する時
- チームメンバーへの教育時

---

## アーキテクチャパターン

### パターンの選択

| パターン | 適用ケース | 詳細ガイド |
|---------|-----------|-----------|
| **MVVM** | 中小規模、SwiftUI | [guides/01-mvvm-pattern.md](guides/01-mvvm-pattern.md) |
| **Clean Architecture** | 大規模、テスタビリティ重視 | [guides/02-clean-architecture.md](guides/02-clean-architecture.md) |
| **VIPER** | 超大規模、チーム開発 | [guides/03-viper-pattern.md](guides/03-viper-pattern.md) |
| **Coordinator** | ナビゲーション複雑 | [guides/04-coordinator-pattern.md](guides/04-coordinator-pattern.md) |

詳細: [guides/00-architecture-overview.md](guides/00-architecture-overview.md)

---

## UI開発

### SwiftUI vs UIKit

| 観点 | SwiftUI | UIKit |
|------|---------|-------|
| 学習曲線 | 緩やか | 急 |
| iOS最低バージョン | iOS 13+ | iOS 2+ |
| カスタマイズ性 | 制限あり | 完全 |
| 推奨ケース | 新規プロジェクト | レガシー、細かい制御 |

### SwiftUI開発

- [guides/05-swiftui-fundamentals.md](guides/05-swiftui-fundamentals.md)
- [guides/06-swiftui-state-management.md](guides/06-swiftui-state-management.md)
- [guides/07-swiftui-animations.md](guides/07-swiftui-animations.md)

### UIKit開発

- [guides/08-uikit-fundamentals.md](guides/08-uikit-fundamentals.md)
- [guides/09-autolayout-mastery.md](guides/09-autolayout-mastery.md)
- [guides/10-custom-controls.md](guides/10-custom-controls.md)

---

## データ管理

### データ永続化の選択

| 技術 | 用途 | ガイド |
|------|------|--------|
| **UserDefaults** | 設定、小さなデータ | [guides/11-userdefaults.md](guides/11-userdefaults.md) |
| **Keychain** | 認証情報、秘密情報 | [guides/12-keychain.md](guides/12-keychain.md) |
| **CoreData** | 複雑なリレーショナルデータ | [guides/13-coredata.md](guides/13-coredata.md) |
| **Realm** | 高速、シンプルなDB | [guides/14-realm.md](guides/14-realm.md) |
| **FileManager** | ファイル、画像 | [guides/15-file-storage.md](guides/15-file-storage.md) |

### ネットワーク通信

- [guides/16-networking-fundamentals.md](guides/16-networking-fundamentals.md)
- [guides/17-api-client-design.md](guides/17-api-client-design.md)
- [guides/18-error-handling.md](guides/18-error-handling.md)

---

## ベストプラクティス

### コーディング規約

→ [references/coding-standards.md](references/coding-standards.md)

### Dependency Injection

→ [references/dependency-injection.md](references/dependency-injection.md)

### 非同期処理

→ [references/async-programming.md](references/async-programming.md)

### メモリ管理

→ [references/memory-management.md](references/memory-management.md)

### エラーハンドリング

→ [references/error-handling-best-practices.md](references/error-handling-best-practices.md)

---

## よくある問題

### クラッシュ・エラー

| 問題 | 原因 | 解決方法 |
|------|------|---------|
| "Thread 1: signal SIGABRT" | 制約エラー、強制アンラップ | [incidents/crashes/](incidents/crashes/) |
| メモリリーク | 循環参照 | [incidents/memory-leaks/](incidents/memory-leaks/) |
| "Could not cast value" | 型キャストエラー | [incidents/type-errors/](incidents/type-errors/) |

詳細: [references/troubleshooting.md](references/troubleshooting.md)

### パフォーマンス問題

→ `ios-performance` Skillを参照

### セキュリティ問題

→ `ios-security` Skillを参照

---

## Agent連携

### このSkillを使用するAgents

1. **architecture-advisor-agent**
   - プロジェクトに最適なアーキテクチャを提案
   - Thoroughness: `thorough`

2. **code-generator-agent**
   - アーキテクチャパターンに従ったコード生成
   - Thoroughness: `medium`

3. **refactoring-agent**
   - 既存コードのリファクタリング提案
   - Thoroughness: `thorough`

4. **troubleshooter-agent**
   - エラー・クラッシュの原因分析と解決策提案
   - Thoroughness: `thorough`

### 推奨Agentワークフロー

#### 新規機能実装時（順次実行）

```
architecture-advisor-agent (設計)
→ code-generator-agent (実装)
→ test-generator-agent (テスト作成)
→ code-review-agent (レビュー)
```

#### トラブルシューティング時（並行実行）

```
troubleshooter-agent +
performance-profiler-agent +
memory-analyzer-agent
→ 結果統合 → 解決策提案
```

---

## クイックリファレンス

### 頻出パターン

#### MVVM ViewModel例

```swift
class UserProfileViewModel: ObservableObject {
    @Published var user: User?
    @Published var isLoading = false
    @Published var errorMessage: String?

    private let userRepository: UserRepositoryProtocol

    init(userRepository: UserRepositoryProtocol) {
        self.userRepository = userRepository
    }

    func fetchUser(id: String) async {
        isLoading = true
        defer { isLoading = false }

        do {
            user = try await userRepository.fetchUser(id: id)
        } catch {
            errorMessage = error.localizedDescription
        }
    }
}
```

#### API Client例

```swift
protocol APIClientProtocol {
    func request<T: Decodable>(_ endpoint: Endpoint) async throws -> T
}

class APIClient: APIClientProtocol {
    func request<T: Decodable>(_ endpoint: Endpoint) async throws -> T {
        let request = try endpoint.asURLRequest()
        let (data, response) = try await URLSession.shared.data(for: request)

        guard let httpResponse = response as? HTTPURLResponse,
              (200...299).contains(httpResponse.statusCode) else {
            throw APIError.invalidResponse
        }

        return try JSONDecoder().decode(T.self, from: data)
    }
}
```

---

## 詳細ドキュメント

### Guides（詳細ガイド）

**アーキテクチャ**
1. [アーキテクチャ概要](guides/00-architecture-overview.md)
2. [MVVMパターン](guides/01-mvvm-pattern.md)
3. [Clean Architecture](guides/02-clean-architecture.md)
4. [VIPERパターン](guides/03-viper-pattern.md)
5. [Coordinatorパターン](guides/04-coordinator-pattern.md)

**UI開発**
6. [SwiftUI基礎](guides/05-swiftui-fundamentals.md)
7. [SwiftUI State管理](guides/06-swiftui-state-management.md)
8. [SwiftUIアニメーション](guides/07-swiftui-animations.md)
9. [UIKit基礎](guides/08-uikit-fundamentals.md)
10. [AutoLayoutマスター](guides/09-autolayout-mastery.md)

**データ管理**
11. [UserDefaults](guides/11-userdefaults.md)
12. [Keychain](guides/12-keychain.md)
13. [CoreData](guides/13-coredata.md)
14. [Realm](guides/14-realm.md)
15. [ファイルストレージ](guides/15-file-storage.md)
16. [ネットワーク基礎](guides/16-networking-fundamentals.md)
17. [APIクライアント設計](guides/17-api-client-design.md)
18. [エラーハンドリング](guides/18-error-handling.md)

### Checklists（チェックリスト）

- [新規プロジェクト開始時](checklists/new-project.md)
- [新機能実装前](checklists/before-feature.md)
- [コードレビュー観点](checklists/code-review.md)
- [リリース前](checklists/pre-release.md)

### Templates（テンプレート）

- [MVVM ViewModel](templates/mvvm-viewmodel.swift)
- [Repository](templates/repository.swift)
- [UseCase](templates/usecase.swift)
- [APIClient](templates/api-client.swift)
- [Coordinator](templates/coordinator.swift)

### References（リファレンス）

- [コーディング規約](references/coding-standards.md)
- [ベストプラクティス集](references/best-practices.md)
- [アンチパターン集](references/anti-patterns.md)
- [Dependency Injection](references/dependency-injection.md)
- [非同期プログラミング](references/async-programming.md)
- [メモリ管理](references/memory-management.md)
- [トラブルシューティング](references/troubleshooting.md)

### Incidents（過去の問題事例）

- [クラッシュ事例](incidents/crashes/)
- [メモリリーク事例](incidents/memory-leaks/)
- [パフォーマンス問題](incidents/performance/)
- [型エラー事例](incidents/type-errors/)

---

## 学習リソース

- 📚 [Swift Programming Language](https://docs.swift.org/swift-book/)
- 📖 [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- 🎥 [WWDC Videos](https://developer.apple.com/videos/)
- 📘 [Ray Wenderlich Tutorials](https://www.raywenderlich.com/)

---

## 関連Skills

- `swiftui-patterns` - SwiftUI特化
- `ios-performance` - パフォーマンス最適化
- `ios-security` - セキュリティ実装
- `testing-strategy` - テスト戦略
- `code-review` - コードレビュー

---

## 更新履歴

このSkill自体の変更履歴は [CHANGELOG.md](CHANGELOG.md) を参照

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaku52) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
