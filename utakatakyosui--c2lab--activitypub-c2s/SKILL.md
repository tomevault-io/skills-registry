---
name: activitypub-c2s
description: ActivityPub Client-to-Server (C2S) インタラクションの実装ガイド。概要とリファレンスを提供する。詳細はサブファイルを参照。 Use when this capability is needed.
metadata:
  author: utakatakyosui
---

# ActivityPub Client-to-Server (C2S) Implementation Guide

## 概要

ActivityPub は、分散型ソーシャルネットワーキングのためのプロトコルです。
このスキルでは、主に **Client-to-Server (C2S)** の実装に焦点を当てます。

詳細な情報は以下のファイルに分割されています。必要に応じて参照してください。

### 📚 詳細ガイド

1.  **[Concepts](./guides/concepts.md)**
    *   JSON-LD & Context (`@context`)
    *   Actor Structure
    *   ID & URI Structure

2.  **[Messaging & Side Effects](./guides/messaging.md)**
    *   Inbox / Outbox Behavior
    *   Server Side Effects (Validation, Persistence, Federation)

3.  **[Activity Types](./guides/activities.md)**
    *   Create, Update, Delete
    *   Follow, Undo
    *   Like, Announce

## 参考リソース

### 仕様書 (W3C)
*   [ActivityPub W3C Recommendation](https://www.w3.org/TR/activitypub/): ActivityPub の公式仕様書。
*   [ActivityStreams 2.0](https://www.w3.org/TR/activitystreams-core/): オブジェクトやActivityの語彙定義。
*   [ActivityStreams Vocabulary](https://www.w3.org/TR/activitystreams-vocabulary/): 利用可能な全プロパティとタイプのリファレンス。

### ガイド & チュートリアル
*   [ActivityPub Rocks!](https://activitypub.rocks/): 実装状況やテストスイートへのリンク集。
*   [Guide for new ActivityPub implementers](https://socialhub.activitypub.rocks/t/guide-for-new-activitypub-implementers/479): SocialHub コミュニティによる実装ガイド。
*   [FediDevs](https://fedidevs.org/): Fediverse 開発者向けのリソース集。

### ツール
*   [FediDB](https://fedidb.org/): Fediverse のソフトウェアとインスタンスの統計。
*   [JSON-LD Playground](https://json-ld.org/playground/): JSON-LD の動作確認とデバッグ。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/utakatakyosui) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
