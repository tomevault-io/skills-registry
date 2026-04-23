---
name: applying-next-js-basic-principles
description: Apply Next.js design principles and best practices for App Router, Server Components, caching strategies, and modern patterns including Next.js 16 updates. Use when building Next.js applications, implementing features, reviewing architecture, migrating to Next.js 16, or when the user mentions Next.js development, components, routing, optimization, or version updates. Use when this capability is needed.
metadata:
  author: camoneart
---

# Applying Next.js Basic Principles

このSkillは『Next.jsの考え方』（AkifumiSato著）に基づいて、Next.jsアプリケーション開発における設計原則とベストプラクティスを提供します。

## 🎯 発動タイミング

このSkillは以下の状況で自動的に発動します：

- Next.jsプロジェクトでの新機能実装時
- App Router / Pages Routerの設計・実装時
- Server Components / Client Componentsの使い分け判断時
- データフェッチング戦略の決定時
- キャッシング戦略の設計時
- パフォーマンス最適化の実施時
- エラーハンドリング・認証の実装時
- Next.js 16への移行・アップグレード時

## 🆕 Next.js 16 アップデート

### [Next.js 16 移行ガイド](next16-updates.md)
2025年10月21日リリースの最新版への対応
- **破壊的変更**: 非同期params/searchParams、Node.js 20.9以上必須
- **新機能**: "use cache"ディレクティブ、updateTag() API、Turbopack標準化
- **移行戦略**: 段階的なアップグレード手順

## 📚 『Next.jsの考え方』の構成

### [Part 1: サーバーコンポーネント基礎](principles/part_1/index.md)
Server Componentsの基本原則と効率的なデータ取得パターン
- Server Componentsの本質
- Request Memoization（リクエストメモ化）
- 並行フェッチとインタラクティブフェッチ
- データローダーパターン
- コロケーション（Colocation）

### [Part 2: クライアントコンポーネント戦略](principles/part_2/index.md)
Client Components設計とパフォーマンス最適化
- Client Componentsのユースケース
- Composition Pattern
- Container/Presentational Pattern
- Container First Design
- バンドル境界の最適化

### [Part 3: キャッシングと動的レンダリング](principles/part_3/index.md)
Next.jsの多層キャッシング戦略
- Static Rendering / Full Route Cache
- Dynamic Rendering / Data Cache
- Router Cache
- Dynamic I/O
- データ更新戦略

### [Part 4: レンダリング最適化](principles/part_4/index.md)
パフォーマンス向上のための高度な技術
- Pure Server Components
- Suspense & Streaming
- Partial Pre-Rendering (PPR)

### [Part 5: 実装技術とベストプラクティス](principles/part_5/index.md)
実践的な実装パターン
- 認証実装パターン
- Request Ref パターン
- エラーハンドリング戦略

## ✅ 実装チェックリスト

新しいNext.js機能を実装する際は、以下の順序で確認：

### 1. コンポーネント設計
- [ ] Server Component として実装可能か検討
- [ ] Client Component が必要な場合、最小限の範囲に限定
- [ ] Composition Pattern でコンポーネントを構成
- [ ] **Next.js 16**: params/searchParamsを非同期でアクセス

### 2. データフェッチング
- [ ] Server Component でのデータ取得を優先
- [ ] Request Memoization の活用
- [ ] 並行フェッチで効率化
- [ ] データローダーパターンの検討

### 3. キャッシング戦略
- [ ] Static Rendering が可能か検討
- [ ] Dynamic Rendering が必要な場合の最適化
- [ ] Router Cache の活用
- [ ] revalidate の適切な設定
- [ ] **Next.js 16**: "use cache"ディレクティブの活用検討

### 4. パフォーマンス最適化
- [ ] Suspense によるストリーミング
- [ ] Loading UI の実装
- [ ] Error Boundary の設置
- [ ] PPR の活用検討
- [ ] **Next.js 16**: Turbopack自動有効化の恩恵

## 🚀 実装例

具体的な実装例は [examples.md](examples.md) を参照してください。

## 📖 詳細ガイド

タスク別の詳細な実装ガイドは [implementation-guide.md](implementation-guide.md) を参照してください。

## 🔗 参考リンク

- [原著者のGitHubリポジトリ](https://github.com/AkifumiSato/zenn-article/tree/main/books/nextjs-basic-principle)
- [Next.js公式ドキュメント](https://nextjs.org/docs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/camoneart) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
