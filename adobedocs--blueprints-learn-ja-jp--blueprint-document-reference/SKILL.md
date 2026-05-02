---
name: blueprint-document-reference
description: Adobe デジタルエクスペリエンスブループリントドキュメントの作成および編集のリファレンスです。 新しいブループリントを作成したり、ブループリントページを追加したりする場合や、ユーザーがブループリントの構造、セクション、テンプレートまたは Adobe Experience League の参照について質問する場合に使用します。 Use when this capability is needed.
metadata:
  author: adobedocs
---


# ブループリントドキュメントの参照

このリポジトリでブループリントドキュメントを作成または編集する際は、このスキルを使用します。 ブループリントは、確立されたビジネス上の問題に対処し、アーキテクチャ図、技術的な考慮事項、および関連する Adobe Experience League ドキュメントへのリンクを含む、反復可能な実装です。

## 適用するタイミング

- 新しいブループリントドキュメントまたはブループリントの概要ページの作成
- 既存のブループリント内のセクションの追加または再構築
- Adobe Experience League ドキュメントへのリンクまたは引用
- 新しいコンテンツとブループリント規則（最前線、見出し、図）の関連付け

## クイックリファレンス

1. **ドキュメントの目的**：ブループリントは、Adobe Experience Platformとアプリケーションの統合方法を示すシステムおよびデータフローアーキテクチャを提供します。 マーケティング部門ではなく、ビジュアル部門と技術部門です。
2. **セクション**：テンプレートの標準セクションを使用します。適用できない場合にのみ省略します（[reference.md](reference.md) を参照）。
3. **Experience League**：製品ドキュメント、API、ガードレール、チュートリアルについては、Experience Leagueよりもリンクをお勧めします。 完全な URL を使用します。URL パターンと形式については [reference.md](reference.md) を参照してください。
4. **リポジトリ構造**：ブループリントは `help/blueprints/` の下に配置されます。 ブループリントページを追加または移動する際に `help/blueprints/TOC.md` を更新します。

## ドキュメント テンプレート

すべてのブループリントページはこの構造に従う必要があります。 該当するセクションのみを含めます。

```markdown
---
title: [Short descriptive title]
description: "[One sentence: what this blueprint shows and why it matters.]"
solution: [Product name, e.g. Real-Time Customer Data Platform, Journey Optimizer]
exl-id: [UUID - leave blank if new, this will be auto-generated as part of the Experience League publishing flow]
---
# [H1 - same as title or expanded]

[1–3 paragraphs: what the blueprint covers, key capabilities, and who it’s for.]

## Applications

* [Product 1]
* [Product 2]

## Use cases

* [Use case 1]
* [Use case 2]

## Prerequisites

[Bullets or short paragraphs: required products, config, or setup.]

## Architecture Diagram

<img src="[path to SVG/image]" alt="[Descriptive alt]" style="width:90%; border:1px solid #4a4a4a" class="modal-image" />

## Guardrails

[Link to Experience League guardrails and any blueprint-specific limits.]

## Implementation patterns

[Optional: named patterns with bullets.]

## Implementation steps

1. [Step with link to Experience League where relevant]
2. ...

## Implementation considerations

[Optional: identity, performance, security, etc.]

## Related documentation

[Grouped links to Experience League: product docs, APIs, tutorials.]
```

概要ページまたはハブページの場合は、概要、ユースケース（またはタブ）、アーキテクチャイメージ、シナリオ/パターンテーブル、前提条件、ガードレール、関連ドキュメントなど、短い構造を使用します。 例については、`help/blueprints/` の既存の概要を参照してください。

## Frontmatter

| フィールド | 必須 | 備考 |
|-------|----------|--------|
| `title` | はい | 短い。Adobe スタイルごとの製品名には `[!DNL Product Name]` を使用します |
| `description` | はい | 1 つの文。検索とカードで使用 |
| `solution` | はい | プライマリ製品（Real-Time Customer Data Platform、Journey Optimizerなど） |
| `exl-id` | はい | UUID。新しいページの場合は空白のままにします |
| `doc-type` | 概要 | メインのブループリントの概要ページに `overview-page` を使用する |
| `kt` | オプション | リンクされている場合は、ナレッジベース記事 ID |

## Adobe Experience League の参照

- **リンクする場合**：製品ドキュメント、API リファレンス、ガードレール、チュートリアル、設定手順については、Experience Leagueにリンクしてください。 長い手順を複製しないでください。要約してリンクします。
- **URL 形式**：完全な URL を使用します。 `https://experienceleague.adobe.com/docs/?lang=ja...` または `https://experienceleague.adobe.com/ja/docs/...` を選択します。 開発者向けドキュメントの場合、`https://developer.adobe.com/...` も有効です。
- **リンクテキスト**：説明テキストを使用します（「ここをクリック」ではなく「[ スキーマを作成 ] (url)」など）。 リンクテキスト内の製品名には、必要に応じて `[!DNL Product Name]` を使用します。
- **関連ドキュメントセクション**：カテゴリ別にリンクをグループ化する「関連ドキュメント」セクションを持つエンドブループリント（宛先設定、SDK ドキュメント、プロファイルとセグメント化、チュートリアルなど）。

URL パターン、リンクのグループ化、例について詳しくは、[reference.md](reference.md) を参照してください。

## 送信前のチェックリスト

- [ ] Frontmatter には、`title`、`description`、`solution`、`exl-id` があります
- [ H1] タイトルと一致する、またはタイトルを適切に展開する
- [ ] アーキテクチャ図の存在と代替テキストの説明
- [ ] 実装手順は、手順が存在するExperience Leagueにリンクしています
- [ ] ガードレール」セクションは、Experience Leagueの公式ガードレールのドキュメントへのリンクです
- [ 関連ドキュメントの節に ]、関連するExperience League リンクが含まれています
- [ ] 新規または移動されたページは `help/blueprints/TOC.md` に反映されます

## その他のリソース

- テンプレートおよびセクションの完全なメモ：[reference.md](reference.md)
- 既存のブループリント：`help/blueprints/` （例：`audience-activation/real-time-lookup.md`、`customer-journeys/journey-optimizer/journey-optimizer-overview.md`）
- 目次とナビゲーション：`help/blueprints/TOC.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adobedocs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
