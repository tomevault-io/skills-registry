---
name: gojiberry-monthly-accounting
description: Gojiberry 事業の毎月の経理精算処理を行うスキル。ユーザーから「Gojiberry の経理処理を始めよう」「Gojiberry の月次精算をお願い」などの依頼があった場合に使用する。Claude Code（`claude --chrome`）または Cowork 環境で、Claude in Chrome を使ったブラウザ操作と Google Sheets API によるデータ入力を実行する。ログイン等ユーザーにしかできない操作は都度依頼する。 Use when this capability is needed.
metadata:
  author: ymd7
---

# Gojiberry Monthly Accounting v7

Gojiberry 事業の毎月の経理精算処理を、Claude が主導して実行する。

## Prerequisites（前提条件）

- Claude Code（`claude --chrome` で起動）または Cowork 環境で実行すること
- Claude in Chrome 拡張機能が有効であること
- Keeper Fill が Chrome にインストール済みであること
- Google Workspace にログイン済みであること
- Google Sheets API 用の Service Account JSON 鍵ファイルがプロジェクトディレクトリ内に配置されていること
- Service Account に Domain-Wide Delegation が有効化されていること
- Google Workspace Admin Console で Service Account のクライアント ID に対して `https://www.googleapis.com/auth/spreadsheets` スコープが承認されていること
- Cowork 環境の場合：ネットワーク allowlist に `oauth2.googleapis.com` と `sheets.googleapis.com` が追加されていること

## Preflight Checks（事前チェック）

Phase 1 に入る前に、以下のチェックを順番に実行する。すべてクリアしてから処理を開始する。

### Check 1: Chrome Connection（Chrome 接続確認）

Claude in Chrome との接続状態を確認する。

- 接続済みの場合 → Check 2 に進む
- 未接続の場合 → ユーザーに「Claude in Chrome が接続されていません。Chrome で Claude 拡張機能が有効になっていることを確認し、接続してください。」と依頼する。接続が確認できるまで待機する。

### Check 2: Downloads Folder Access（ダウンロードフォルダのアクセス確認）

このワークフローでは PDF や CSV をダウンロードするため、ダウンロードフォルダへのアクセスが必要。

- Claude Code の場合：ダウンロードフォルダ（通常 `~/Downloads`）にアクセスできることを確認する
- Cowork の場合：ダウンロードフォルダを作業フォルダとして追加するようユーザーに依頼する
- アクセスが確認できたら → Check 3 に進む

### Check 3: Sheets API Authentication（Sheets API 認証確認）

プロジェクトディレクトリ内の Service Account JSON 鍵ファイルを使い、Domain-Wide Delegation で Google Sheets API への認証を確認する。

- 鍵ファイルを検出する
- ユーザーに「Sheets API の操作に使用する Google Workspace アカウントのメールアドレスを教えてください。」と確認する
- `subject` にユーザーのメールアドレスを指定して `oauth2.googleapis.com` でアクセストークンを取得する
- 対象スプレッドシートのメタデータ（シート名一覧）を取得して読み取りを確認する
- 認証成功の場合 → Check 完了。以降の Sheets API 操作はすべてこの `subject` で実行する
- 失敗の場合 → エラー内容をユーザーに通知し、Prerequisites（Domain-Wide Delegation の設定）を確認するよう依頼する

### Preflight Complete

すべてのチェックが完了したら：

1. ユーザーに「経理部門シートの『記入者』カラムに記入する名前を教えてください。」と確認する
2. 対象月の確認を行う（Common: Month Calculation 参照）
3. 作業フォルダを作成する（Workspace Setup 参照）
4. 監査ログを初期化する（Audit Log 参照）
5. 「事前チェック完了。経理処理を開始します。」と通知し、Phase 1 に進む

---

## Target Services（対象サービス）

| Service | Type | Login URL | Billing URL |
|---|---|---|---|
| Shopify Partner | Revenue | https://partners.shopify.com/ | https://partners.shopify.com/2163100/payments |
| Bakuraku Card | Expense | https://card.layerx.jp/ | (dynamic, see below) |

## Execution Order（実行順序）

必ず以下の順序で実行する：

1. **Phase 1: Revenue Processing** — Shopify Partner（売上処理）
2. **Phase 2: Expense Processing** — Bakuraku Card（経費処理）

各 Phase の開始時にユーザーに「Phase X を開始します」と通知する。

## Common: Login Handling（共通：ログイン処理）

各サービスのページを開いた際、以下の判定を行う：

1. ページ内容からログイン状態を判定する
2. **ログイン済みの場合** → そのままデータ取得を続行
3. **未ログイン（ログインページにリダイレクトされた等）の場合：**
   - ユーザーに「{サービス名} にログインが必要です。Keeper Fill でログインを承認してください。完了したら OK と教えてください。」と伝える
   - ユーザーの「OK」を待つ
   - ログイン後のページを確認し、データ取得を続行

**重要：ログインフォームにパスワードを直接入力してはならない。Keeper Fill がフォームを自動入力する。**

## Common: Month Calculation（共通：対象月の計算）

経理処理は月初に前月分を処理する。現在日付から前月を自動計算する。

- 現在が 2026年2月 → 対象月は 2026年1月
- 処理開始時にユーザーに「対象月: {YYYY年M月} で処理します。変更する場合は教えてください。」と確認する

## Common: Workspace Setup（共通：作業フォルダ）

対象月の確定後、プロジェクトディレクトリ内に作業フォルダを作成する。

```
{プロジェクトディレクトリ}/
└── {YYYY-MM}/              ← 作業フォルダ（対象月）
    ├── downloads/          ← ダウンロードファイル保管
    └── audit.log           ← 監査ログ
```

- フォルダ名は対象月の `YYYY-MM` 形式（例：`2026-01`）
- `downloads/` サブフォルダを作成する
- 作業中にダウンロードした PDF・CSV はすべて `downloads/` フォルダに移動して保管する

## Common: Audit Log（共通：監査ログ）

作業の監査・追跡を目的として、作業フォルダ内に `audit.log` を作成する。後から「何をどう判断し、何を書き込んだか」を辿れることが目的。

### ログフォーマット

```
[YYYY-MM-DD HH:MM:SS] CATEGORY: message
```

CATEGORY は以下のいずれか：
- `INIT` — 作業開始・パラメータ確定
- `SELECT` — データ選択の判断根拠
- `EXTRACT` — ソースからのデータ抽出結果
- `WRITE` — スプレッドシートへの書き込み実行
- `COMPLETE` — Phase/作業完了

### 記録タイミングと内容

以下のタイミングで、以下の内容を記録する：

**INIT（Preflight Complete 時）:**
```
[timestamp] INIT: target_month=2026-01, operator={記入者名}, subject={メールアドレス}, workspace={作業フォルダパス}
```

**SELECT（Step 1-2 完了時 — Payout 選択後）:**
```
[timestamp] SELECT: shopify_payouts selected={件数} entries, payout_dates=[{日付1}, {日付2}]
```

**EXTRACT（Step 1-3 完了時 — PDF データ抽出後）:**
```
[timestamp] EXTRACT: payout {payout_date}: USD={金額}, JPY={金額}
```
（Payout ごとに1行ずつ）

**WRITE（Step 1-4 実行後 — Revenue Raw Data 書き込み後）:**
```
[timestamp] WRITE: revenue_raw_data sheet={シート名}, rows_written={件数}, range={書き込み範囲}
```

**WRITE（Step 1-6 実行後 — 経理シート転記後）:**
```
[timestamp] WRITE: accounting_billing sheet=請求依頼, spreadsheet={ID}, rows_written={件数}, start_row={行番号}
```

**SELECT（Step 2-2 完了時 — CSV ダウンロード後）:**
```
[timestamp] SELECT: bakuraku_csv file={ファイル名}, month={対象月}
```

**EXTRACT（Step 2-3 完了時 — CSV データ抽出後）:**
```
[timestamp] EXTRACT: bakuraku_expenses entries={件数}, total_amount={合計金額}JPY
```

**WRITE（Step 2-4 実行後 — Expenses Raw Data 書き込み後）:**
```
[timestamp] WRITE: expenses_raw_data sheet={シート名}, rows_written={件数}, range={書き込み範囲}
```

**WRITE（Step 2-6 実行後 — 経理シート転記後）:**
```
[timestamp] WRITE: accounting_payment sheet=支払依頼, spreadsheet={ID}, rows_written={件数}, start_row={行番号}
```

**COMPLETE（全 Phase 完了時）:**
```
[timestamp] COMPLETE: all phases finished, revenue_entries={件数}, expense_entries={件数}
```

---

## Phase 1: Revenue Processing（売上処理）

### Step 1-1: Open Shopify Partner Payments Page

Claude in Chrome で以下の URL を開く：

```
https://partners.shopify.com/2163100/payments
```

ログイン判定を行い、必要であればログイン処理を実施する。

### Step 1-2: Download Payout PDFs

支払い一覧ページが表示されたら：

1. 対象月分のデータを特定する。「YYYY年MM月DD日に送信されました」の日付（= Payout date）が**対象月内**にあるものを選択する。販売期間ではなく送信日で判断すること。
   - 例：対象月が 2026年1月の場合、「2026年1月8日に送信されました」「2026年1月22日に送信されました」の2件を選択する。「2026年2月6日に送信されました」は対象外。
2. 対象月分は通常 **2件**（半月ごと）ある
3. 各データをクリック → 詳細ページに遷移
4. 「ダウンロードする」ボタン → 下向き矢印アイコンをクリック
5. 「支払い明細書をダウンロード」を選択 → PDF を取得
6. 2件目も同様にダウンロード

**ダウンロードが困難な場合：**
ユーザーに「Shopify Partner の支払い明細 PDF を手動でダウンロードしてください。完了したら OK と教えてください。」と依頼する。

### Step 1-3: Extract Data from PDFs

ダウンロードした PDF から以下の情報を抽出する：

| Field | Label on PDF |
|---|---|
| Payout date | 支払い日 |
| Total amount (USD) | 支払い合計 |
| Total amount in local currency (JPY) | 現地通貨の合計額 |

2件の PDF からそれぞれ抽出する。

### Step 1-4: Input to Revenue Google Sheet

Google Sheets API を使い、以下のスプレッドシートに直接書き込む：

- **Spreadsheet ID**: `1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc`
- **Sheet**: Revenue Raw Data
- **参照 URL**: https://docs.google.com/spreadsheets/d/1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc/edit?gid=347991274#gid=347991274

Sheets API の `spreadsheets.values.append` を使い、シート末尾に以下のフォーマットで追記する：

```
{Payout date}	{Total amount USD 数値のみ}	{Total amount JPY 数値のみ}
```

例：
```
2025-12-05	2077.17	319482
2025-12-22	1272.95	199229
```

**書き込み前に、ユーザーに入力内容を表示して確認を求める。確認後に API を実行する。**

### Step 1-5: Verify Billing Request Sheet

Revenue Raw Data への入力が完了すると、以下のシートに自動反映される：

- **Sheet**: 請求依頼 in EDOCODE 売上管理
- **参照 URL**: https://docs.google.com/spreadsheets/d/1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc/edit?gid=1670535168#gid=1670535168

Sheets API の `spreadsheets.values.get` を使い、新しいデータが反映されていることを確認する。
ユーザーにも反映結果を表示して確認を求める。

### Step 1-6: Transfer to Accounting Sheet

Sheets API を使い、「請求依頼 in EDOCODE 売上管理」の該当行データを読み取り、経理部門用シートに書き込む。

**Read from（読み取り元）:**
- **Spreadsheet ID**: `1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc`
- **Sheet**: 請求依頼 in EDOCODE 売上管理
- **参照 URL**: https://docs.google.com/spreadsheets/d/1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc/edit?gid=1670535168#gid=1670535168

**Write to（書き込み先）:**
- **Spreadsheet ID**: `1Ru-_SwYdD190XDmKc9nCm3T9Y5oQuxYnEq_rOh6hrhI`
- **Sheet**: 請求依頼
- **参照 URL**: https://docs.google.com/spreadsheets/d/1Ru-_SwYdD190XDmKc9nCm3T9Y5oQuxYnEq_rOh6hrhI/edit?gid=575191542#gid=575191542

**書き込み方法:**
経理部門用シートには数式が入った空行が存在するため、`spreadsheets.values.append` は使わない。代わりに以下の手順で `spreadsheets.values.update`（PUT）を使う：
1. シート全体を読み取り、実データが入っている最終行を特定する（PJ列が空でない最後の行）
2. その次の行を起点として `values.update` で書き込む

**書き込みデータには「記入者」カラムにも、作業開始時にユーザーに確認した名前を含める。**

転記前にユーザーに内容を表示して確認を求める。確認後に API を実行する。

**Phase 1 完了。ユーザーに「売上処理が完了しました。経費処理に進みます。」と通知する。**

---

## Phase 2: Expense Processing（経費処理）

### Step 2-1: Open Bakuraku Card Page

対象月から URL を動的に生成し、Claude in Chrome で開く：

```
https://card.layerx.jp/cards/01H6B18YEZWDT2669FH4CR5PXZ?tabKey=clearingTransactions&searchDateType=REGISTERED_AT&dateMonth={対象月の月}&dateYear={対象月の年}&memoIsBlank=false&currentPage=1
```

例：対象月が 2026年1月の場合
```
https://card.layerx.jp/cards/01H6B18YEZWDT2669FH4CR5PXZ?tabKey=clearingTransactions&searchDateType=REGISTERED_AT&dateMonth=1&dateYear=2026&memoIsBlank=false&currentPage=1
```

ログイン判定を行い、必要であればログイン処理を実施する。

**Fallback URLs（上記 URL で問題がある場合）：**
1. カード詳細ページ: https://card.layerx.jp/cards/01H6B18YEZWDT2669FH4CR5PXZ
2. トップページ: https://card.layerx.jp/cards

フォールバック URL を使用した場合は、ユーザーに「年月プルダウンで {対象年}年 {対象月}月 に切り替えてください。」と依頼する。

### Step 2-2: Download CSV

確定明細ページが表示されたら：

1. 表示されている明細が対象月分であることを確認
2. 「CSV出力」を含むボタン（例：「該当データのCSV出力」）を探し、`read_page` の ref を使ってクリックする
3. クリック後、「明細のCSV出力」モーダルダイアログ（エンコーディング選択と「CSV出力」送信ボタンを含む）が表示されるか確認する
4. **モーダルが表示されない場合：** ref ベースのクリックではダイアログがトリガーされないことがある。その場合は `javascript_tool` で `document.querySelector` を使ってボタン要素を見つけ、`.click()` で直接クリックする
5. モーダルが表示されたら、エンコーディングは「エクセル対応 (UTF8-BOM)」のままでよい
6. ダイアログ内の「CSV出力」送信ボタンを `read_page` の ref でクリック → CSV がダウンロードされる

**ダウンロードが困難な場合：**
ユーザーに「バクラクカードの CSV を手動でダウンロードしてください。完了したら OK と教えてください。」と依頼する。

### Step 2-3: Extract Data from CSV

ダウンロードした CSV から以下のカラムを抽出する：

| Column（使用する） |
|---|
| 取引ID |
| 取引関連ID |
| 利用日時 |
| 確定日時 |
| カードID |
| カード名 |
| カードタグ |
| 取引内容 |
| メモ |
| ステータス |
| 金額 |
| 外貨コード |
| 外貨金額 |
| 当初取引内容 |
| 当初金額 |
| 当初外貨コード |
| 当初外貨金額 |

**除外するカラム：** 海外事務手数料、カードカテゴリ、明細種別

### Step 2-4: Input to Expenses Google Sheet

Google Sheets API を使い、以下のスプレッドシートに直接書き込む：

- **Spreadsheet ID**: `1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc`
- **Sheet**: Expenses Raw Data
- **参照 URL**: https://docs.google.com/spreadsheets/d/1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc/edit?gid=1698621903#gid=1698621903

Sheets API の `spreadsheets.values.append` を使い、各行を上記カラム順でシート末尾に追記する。

**書き込み前に、ユーザーに入力内容を表示して確認を求める。確認後に API を実行する。**

### Step 2-5: Verify Payment Request Sheet

Expenses Raw Data への入力が完了すると、以下のシートに自動反映される：

- **Sheet**: 支払依頼 in EDOCODE 売上管理
- **参照 URL**: https://docs.google.com/spreadsheets/d/1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc/edit?gid=1515612658#gid=1515612658

Sheets API の `spreadsheets.values.get` を使い、新しいデータが反映されていることを確認する。
ユーザーにも反映結果を表示して確認を求める。

### Step 2-6: Transfer to Accounting Sheet

Sheets API を使い、「支払依頼 in EDOCODE 売上管理」の該当行データを読み取り、経理部門用シートに書き込む。

**Read from（読み取り元）:**
- **Spreadsheet ID**: `1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc`
- **Sheet**: 支払依頼 in EDOCODE 売上管理
- **参照 URL**: https://docs.google.com/spreadsheets/d/1sKGTyNMiwCI3ORvSzdvVI1LexHjKIP3lcgYYztXtzWc/edit?gid=1515612658#gid=1515612658

**Write to（書き込み先）:**
- **Spreadsheet ID**: `1Ru-_SwYdD190XDmKc9nCm3T9Y5oQuxYnEq_rOh6hrhI`
- **Sheet**: 支払依頼
- **参照 URL**: https://docs.google.com/spreadsheets/d/1Ru-_SwYdD190XDmKc9nCm3T9Y5oQuxYnEq_rOh6hrhI/edit?gid=1609046542#gid=1609046542

**書き込み方法:**
経理部門用シートには数式が入った空行が存在するため、`spreadsheets.values.append` は使わない。代わりに以下の手順で `spreadsheets.values.update`（PUT）を使う：
1. シート全体を読み取り、実データが入っている最終行を特定する（PJ列が空でない最後の行）
2. その次の行を起点として `values.update` で書き込む

**書き込みデータには「記入者」カラムにも、作業開始時にユーザーに確認した名前を含める。**

転記前にユーザーに内容を表示して確認を求める。確認後に API を実行する。

**Phase 2 完了。**

---

## Completion（完了処理）

全 Phase 完了後、ユーザーに以下のサマリーを表示する：

```
## Gojiberry 月次経理精算 完了サマリー

対象月: {YYYY年M月}

### 売上処理
- Shopify Partner PDF: {件数}件ダウンロード
- Revenue Raw Data: {件数}行追記
- 経理部門シートへの転記: 完了

### 経費処理
- バクラクカード CSV: ダウンロード完了
- Expenses Raw Data: {件数}行追記
- 経理部門シートへの転記: 完了
```

## Security Notes（セキュリティに関する注意事項）

- **パスワードやログイン認証情報を会話コンテキストに出力してはならない**
- ログインは必ず Keeper Fill 経由で行い、Claude がフォームに直接入力しない
- **Service Account の JSON 鍵ファイルの内容を会話コンテキストに出力してはならない**
- Google Sheets API への書き込みは、必ずユーザーの確認を得てから実行する
- Google Sheet の URL やデータ内容は業務上必要な範囲でのみ表示する
- PDF や CSV に含まれる個人情報・機密情報は必要最小限の抽出にとどめる

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ymd7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
