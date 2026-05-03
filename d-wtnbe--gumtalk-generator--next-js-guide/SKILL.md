---
name: next-js-guide
description: Next.js (App Router) 開発における基本的なガイドラインとコーディング規約 Use when this capability is needed.
metadata:
  author: D-Wtnbe
---

# Next.js 開発ガイド

このスキルは、Next.jsプロジェクトにおいて一貫性のあるコードを実装するためのガイドラインを提供します。
コンポーネントの作成やデータフェッチの際に、以下のルールを確認してください。

## 1. コンポーネント設計

- **Server Components の優先**: デフォルトですべてのコンポーネントを Server Components (RSC) として作成してください。クライアントの対話性（Hooks, Event Listeners）が必要な場合のみ `"use client"` ディレクティブを追加して Client Components にしてください。
- **機能ごとの分割**: `src/app` 内にはルーティング関連のファイルのみを置き、再利用可能なコンポーネントは `src/components` の適切なディレクトリ（ex. `ui`, `features`, `layouts`）に配置してください。

## 2. データフェッチ

- **サーバーサイドでのデータ取得**: データベースや外部APIへのアクセスは、できる限り Server Components 内で行うこと。
- **キャッシュの活用**: Next.jsの `fetch` や `cache` (React) 関数を使用して、不要なリクエストを防ぎパフォオーマンスを向上させてください。

## 3. スタイリング

- **Vanilla CSS / CSS Modules**: 必要に応じて CSS Modules (`.module.css`) を使用し、コンポーネントレベルでスタイルをスコープ化してください。Tailwind等が導入されている場合はそちらのベストプラクティスを遵守すること。

## 参考
- [Next.js Documentation](https://nextjs.org/docs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/D-Wtnbe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
