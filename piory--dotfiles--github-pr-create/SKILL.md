---
name: github-pr-create
description: Use when users ask for PR creation, PR draft creation, publishing changes, or only drafting PR title/body before opening a PR. Follow the repository template and recent PR format via GitHub MCP.
metadata:
  author: piory
---

# GitHub PR 作成 (GitHub MCP)

## 目的
GitHub MCP を使って、リポジトリの PR テンプレートと既存 PR のフォーマットに合わせた PR タイトル/本文を作成し、必要時に PR を作成する。

## 適用トリガー
- PR を作成したい
- Draft PR を作りたい
- 変更を GitHub に公開したい
- PR を出す前に、タイトル/説明文だけ先に考えたい

## 必須方針
- GitHub MCP を使って情報取得・作成を行う。
- PR テンプレートがある場合は必ず従う。
- 直近の PR を参照してタイトル/本文の形式を合わせる。
- 不明な点はユーザーに確認する。
- 機密情報は含めない。
- ユーザーが「文章作成のみ」を求めた場合は、PR作成APIは実行しない。

## 手順
1) 対象リポジトリ・ブランチ・ベースブランチを確認する
   - ユーザーの指定がない場合は質問する。
2) 変更内容を把握する
   - 差分、変更ファイル、主要な変更点を整理する。
3) PR テンプレートを探す
   - `.github/PULL_REQUEST_TEMPLATE*` や `.github/pull_request_template.md` などを GitHub MCP で確認する。
4) 直近の PR フォーマットを把握する
   - 同一リポジトリの直近の PR を数件取得し、タイトル/本文の構成を確認する。
5) PR タイトルと本文を作成する
   - テンプレートと既存 PR の形式に合わせる。
   - 変更内容・影響範囲・確認手順を簡潔に記載する。
6) 実行モードを分岐する
   - 文章作成のみ: 作成したタイトル/本文を提示して終了する。
   - PR作成まで: GitHub MCP で PR を作成する。
7) PR作成まで実行する場合
   - draft かどうか、レビューア、ラベル等は必要に応じてユーザーに確認する。
8) 作成後の確認
   - PR URL を提示し、必要な追加情報（レビュー依頼など）を確認する。

## 迷ったときの確認事項
- ベースブランチはどれか
- draft か通常 PR か
- レビューア/ラベル/マイルストーンの要否
- テンプレートに書く検証コマンド

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/piory) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
