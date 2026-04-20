---
name: documentation-maintenance-ja
description: ドキュメントを継続的に監査・更新し、反復するほど品質を上げる運用スキル。doc/配下の設計書・規約・ADR・ガイドラインを更新するときに使う。特に、命名規則の統一、Source of Truth同期、実装整合、公式根拠URL明記、リンク整合、重複削減、レビュー可能性向上、改善バックログ運用を日本語で実施する場合に使う。 Use when this capability is needed.
metadata:
  author: segnities007
---

# ドキュメント更新運用スキル（日本語・継続改善版）

## 到達目標（100% / 200% / 300%）

- 100%: 正確で矛盾がない（壊れていない）
- 200%: 変更に強く、レビューしやすい（運用可能）
- 300%: 反復するほど品質が上がる仕組みがある（自己改善可能）

## 基本原則

- `decisions/` を正本として扱う。
- 変更時は公式根拠URLを明記する。
- 実装変更と規約変更を同一PRで同期する。
- 1回で完璧を狙わず、反復サイクルで品質を上げる。
- 独立した監査（スコア計測・リンク確認・差分抽出）は並列実行を優先する。
- サブエージェント機能が使える場合は、監査対象を分割して並行分析し、最終結果を統合する。

## 成熟度モデル（必須）

### Level 1: Correctness（100%）

- 誤記・矛盾・壊れリンクを除去する。
- `Last Updated` と `Source of Truth` を揃える。
- 旧命名・旧設計語を排除する。

### Level 2: Operability（200%）

- 同期対象ファイルを明示する。
- レビュー観点をドキュメントに埋め込む。
- 変更手順を再現可能なコマンドで示す。

### Level 3: Self-Improving（300%）

- 採点（スコア）を毎回記録する。
- 改善バックログを優先度付きで管理する。
- 次回ループの改善仮説を残す。

## 品質ルーブリック（0-5点, 合計50点）

1. 正確性（実装と一致）
2. 一貫性（命名・用語・構成）
3. 完結性（必要情報の欠落なし）
4. 根拠性（公式URLの明示）
5. 保守性（変更時の追従先が明確）
6. 検証性（チェック手順がある）
7. 可読性（短く明瞭、見出し構造適切）
8. 発見性（索引・導線がある）
9. 非重複性（同義ルールの乱立なし）
10. 進化性（改善履歴と次アクションがある）

採点基準:
- 0-19: 要再設計
- 20-34: 基礎品質
- 35-44: 実運用品質
- 45-50: 継続改善品質

## 反復サイクル（毎回実施）

1. 計測する
- `scripts/doc_excellence_score` を実行する。
- 現在スコアと主要欠陥を把握する。

2. 影響範囲を特定する
- `rg` で旧命名、矛盾語、未更新メタデータを抽出する。
- SoTと索引への波及を列挙する。

3. 根拠を確保する
- 最低2件の公式ソースを参照する。
- 「事実（外部根拠）」と「採用判断（プロジェクト方針）」を分離する。

4. 最小差分で更新する
- 正本→索引→運用ルールの順で更新する。
- 例示コードの命名と実装を一致させる。

5. 再検証する
- 旧命名ヒットがゼロか確認する。
- 主要リンク・参照が生きているか確認する。

6. スコア再計測する
- 変更後スコアを再取得し、差分を記録する。

7. 次ループを定義する
- `scripts/doc_improvement_backlog` で課題を生成する。
- 次回上位3件を選んで優先実行する。

## 実行コマンド

```bash
# 1) スコア測定
skills/documentation-maintenance-ja/scripts/doc_excellence_score .

# 2) 旧命名・旧設計語検出
rg -n "register[A-Za-z]+Entry|NavigationEntry\.kt|NavHost に登録|唯一の public API は Builder 関数" doc

# 3) 改善バックログ生成
skills/documentation-maintenance-ja/scripts/doc_improvement_backlog . > /tmp/doc_backlog.md
```

## 変更時に必ず同期する対象

- `doc/README.md`
- `doc/decisions/standards/doc-operation-rules.md`
- 実装規約（`doc/decisions/engineering/*.md`）
- 設計規約（`doc/decisions/architecture/*.md`, `doc/decisions/design/*.md`）
- 該当ADR（意思決定に変更がある場合）

## 優先順位づけ

- 優先度A: 誤情報、矛盾、実装不整合
- 優先度B: 命名揺れ、同期漏れ、リンク欠落
- 優先度C: 表現改善、構造最適化、重複削減

AをゼロにしてからB、Bを安定させてからCを進める。

## 参照ファイル

- `references/best-practices-sources-ja.md`
- `references/excellence-iteration-playbook-ja.md`
- `references/review-rubric-ja.md`
- `references/official-source-priority-ja.md`
- `references/anti-patterns-ja.md`
- `scripts/doc_sync_audit`
- `scripts/doc_excellence_score`
- `scripts/doc_improvement_backlog`
- `scripts/doc_iteration_run`
- `scripts/doc_release_gate`
- `scripts/doc_link_lint`
- `scripts/doc_sot_map`
- `scripts/doc_score_history`

## 出力フォーマット（毎回固定）

- 今回の目標（100/200/300のどれを狙うか）
- 変更方針（3行以内）
- 修正内容（ファイル単位）
- スコア（Before/After）
- 根拠URL（公式のみ）
- 次ループ上位3課題

## 高速ループ実行

```bash
skills/documentation-maintenance-ja/scripts/doc_iteration_run . /tmp/doc-iteration
```

このコマンドで、スコア・バックログ・次アクション要約を一括生成する。

## さらに上を目指す運用（推奨）

```bash
# リリース前ゲート（失敗時は非0終了）
skills/documentation-maintenance-ja/scripts/doc_release_gate .

# SoT依存マップを可視化
skills/documentation-maintenance-ja/scripts/doc_sot_map . > /tmp/doc-sot-map.md

# スコア履歴を蓄積（トレンド管理）
skills/documentation-maintenance-ja/scripts/doc_score_history .
```

## ゲート基準（推奨）

- `stale_naming_hits == 0`
- `missing_last_updated == 0`
- `missing_source_of_truth == 0`
- `score >= 35`（L2-200%以上）
- `doc_link_lint(active) == OK`

## リンクLintのスコープ

- `active`: `doc/README.md` と `doc/decisions/**` のみ検査（通常運用向け）
- `all`: `doc/**` 全体を検査（大掃除向け）

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/segnities007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
