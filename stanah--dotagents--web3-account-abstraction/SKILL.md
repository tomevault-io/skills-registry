---
name: web3-account-abstraction
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# web3-account-abstraction: アカウント抽象化（ERC-4337）スキル

ERC-4337 に基づくアカウント抽象化の設計パターン、スマートアカウント実装、Paymaster 統合、セッションキー管理を提供するスキル。ガスレストランザクション、ソーシャルリカバリー、バッチトランザクション等のユースケースを対象とする。

## ERC-4337 アーキテクチャ概要

### コンポーネント関係

```
ユーザー
  │
  ├─ UserOperation 作成
  │
  ▼
Bundler (alt-mempool)
  │
  ├─ UserOp をバンドル
  │
  ▼
EntryPoint コントラクト (singleton)
  │
  ├─ validateUserOp() → Smart Account
  ├─ validatePaymasterUserOp() → Paymaster (optional)
  └─ execute() → Smart Account → Target Contract
```

### 主要コンポーネント

| コンポーネント | 役割 | オンチェーン/オフチェーン |
|--------------|------|----------------------|
| Smart Account | ユーザーのウォレット（コントラクト） | オンチェーン |
| EntryPoint | UserOp のバリデーション・実行を管理 | オンチェーン（singleton） |
| Paymaster | ガス代を代払いする | オンチェーン |
| Bundler | UserOp を収集・バンドル・送信 | オフチェーン |
| Account Factory | Smart Account のデプロイ | オンチェーン |

### UserOperation の構造

```
UserOperation {
  sender          // Smart Account アドレス
  nonce           // リプレイ防止
  initCode        // 初回: Factory + 作成データ / 以降: 空
  callData        // 実行する関数呼び出し
  callGasLimit    // 実行時ガスリミット
  verificationGasLimit  // バリデーション時ガスリミット
  preVerificationGas    // Bundler へのオーバーヘッド補償
  maxFeePerGas
  maxPriorityFeePerGas
  paymasterAndData      // Paymaster アドレス + 検証データ
  signature             // Smart Account の署名
}
```

## ワークフロー

### Step 1: AA 要件の整理

ユーザーの要件から AA のユースケースを判定する:

| ユースケース | 必要コンポーネント | 参照リファレンス |
|------------|-----------------|----------------|
| ガスレストランザクション | Paymaster | `references/paymaster-patterns.md` |
| ソーシャルリカバリー | Smart Account（Guardian） | `references/smart-account-patterns.md` |
| セッションキー（自動実行） | Session Key Module | `references/session-keys.md` |
| バッチトランザクション | Smart Account（executeBatch） | `references/erc4337-core.md` |
| ERC20 でガス支払い | Token Paymaster | `references/paymaster-patterns.md` |

**検証ゲート**: ユースケースが上記のいずれかに該当すること。該当しない場合は AskUserQuestion で要件を明確化する。AA が不要な場合（通常の EOA で十分な場合）はその旨を伝える。

### Step 2: スマートアカウント選択

プロジェクト要件に基づきスマートアカウント実装を選択する:

| 実装 | 特徴 | 推奨ケース |
|------|------|----------|
| Safe (旧 Gnosis Safe) | 最も実績あり。モジュラー設計。マルチシグ対応 | エンタープライズ、高セキュリティ要件 |
| Kernel (ZeroDev) | 軽量。プラグイン型。低ガスコスト | DeFi、ゲーム、高頻度操作 |
| Biconomy Smart Account | SDK が充実。導入が容易 | 迅速なプロトタイプ、モバイルアプリ |
| Simple Account (eth-infinitism) | リファレンス実装。最小構成 | 学習・検証、カスタム実装のベース |

`references/smart-account-patterns.md` を読み込み、選択した実装の詳細パターンを確認する。

**判断が不明な場合**: AskUserQuestion で「セキュリティ重視 vs 開発速度重視」「マルチシグ必要性」を確認する。

**検証ゲート**: スマートアカウント実装が決定し、対応する SDK / ライブラリがプロジェクトにインストール可能であること。

### Step 3: リファレンス読み込み

Step 1-2 の結果に基づき、必要なリファレンスを読み込む:

| リファレンス | 読み込む条件 |
|------------|------------|
| `references/erc4337-core.md` | 常に読み込む（基礎知識） |
| `references/paymaster-patterns.md` | ガスレス / ERC20 支払いを実装する場合 |
| `references/smart-account-patterns.md` | スマートアカウントのカスタマイズが必要な場合 |
| `references/session-keys.md` | セッションキー / 自動実行を実装する場合 |

**検証ゲート**: `erc4337-core.md` が正常に読み込めること。

### Step 4: 実装

1. **スマートアカウントのセットアップ**:
   - Account Factory の選択またはデプロイ
   - 初回デプロイ時の `initCode` の構成
   - 署名スキーム（ECDSA / Passkey / マルチシグ）の設定
2. **UserOperation の構成**:
   - `callData` のエンコード（`execute` / `executeBatch`）
   - ガスパラメータの見積もり（Bundler RPC 使用）
   - Paymaster 使用時の `paymasterAndData` 設定
3. **Bundler への送信**:
   - `eth_sendUserOperation` RPC コール
   - UserOp ハッシュの取得と追跡
4. **フロントエンド統合**:
   - `web3-frontend` スキルの wagmi / viem パターンと組み合わせる
   - トランザクション UX はガスレスに適応させる

**検証ゲート**: UserOperation が Bundler に受理され、EntryPoint で実行されること。テストネット（Sepolia）で検証する。

### Step 5: テスト・デプロイ

1. **ローカルテスト**:
   - anvil + Bundler（Stackup / Pimlico のローカルモード）でテスト
   - UserOp のバリデーション・実行を確認
2. **テストネットデプロイ**:
   - Sepolia でスマートアカウントのデプロイ + 操作を検証
   - Paymaster のデポジット確認
3. **本番チェックリスト**:
   - EntryPoint アドレスの正当性確認（公式 singleton）
   - Paymaster のデポジット残高モニタリング設定
   - Bundler のフェイルオーバー設定

**検証ゲート**: テストネットで全ユースケースが正常動作すること。

## 使用例

### 例 1: ガススポンサー（ユーザーのガス代を dApp が負担）

**ユーザー入力**: 「ユーザーがガス代を払わなくて済むようにしたい」

**アクション**:
1. Step 1: ガスレストランザクション → Paymaster が必要
2. Step 2: 開発速度重視 → Biconomy Smart Account を選択
3. Step 3: `erc4337-core.md` + `paymaster-patterns.md` を読み込み
4. Step 4: 以下を実装:
   - Biconomy SDK でスマートアカウント初期化
   - Verifying Paymaster の設定（dApp サーバーで署名生成）
   - `paymasterAndData` を UserOp に付与
   - フロントエンドではガス見積もり表示を省略（0 ETH）
5. Step 5: Sepolia で Paymaster デポジット → ガスレスミントを検証

**結果**: ユーザーは ETH を持っていなくても NFT をミントできる。ガス代は dApp の Paymaster が負担。

### 例 2: セッションキー（ゲームの自動実行）

**ユーザー入力**: 「ブロックチェーンゲームで、毎回署名しなくて済むようにしたい」

**アクション**:
1. Step 1: セッションキー → Session Key Module が必要
2. Step 2: 高頻度操作 → Kernel (ZeroDev) を選択
3. Step 3: `erc4337-core.md` + `session-keys.md` を読み込み
4. Step 4: 以下を実装:
   - Kernel Smart Account のセットアップ
   - Session Key Module のインストール
   - セッションキーの権限スコーピング（対象コントラクト・関数・期限を制限）
   - ブラウザのローカルストレージにセッションキーを保存
   - セッションキーで UserOp に自動署名
5. Step 5: ゲームコントラクトへの move() 関数をセッションキーで100回実行して検証

**結果**: 初回のみマスターキーで署名。以降はセッションキーで自動署名され、ゲーム操作がシームレス。

### 例 3: ソーシャルリカバリー

**ユーザー入力**: 「秘密鍵を紛失してもアカウントを復旧できるようにしたい」

**アクション**:
1. Step 1: ソーシャルリカバリー → Guardian 機能が必要
2. Step 2: セキュリティ重視 → Safe を選択
3. Step 3: `erc4337-core.md` + `smart-account-patterns.md` を読み込み
4. Step 4: 以下を実装:
   - Safe Smart Account のセットアップ（1/1 署名者）
   - Recovery Module の追加: 3 人の Guardian を設定（2/3 で復旧可能）
   - Guardian には友人の EOA アドレスまたは信頼されたサービスを設定
   - 復旧フロー: Guardian がリカバリー提案 → 遅延期間（48時間）→ 新オーナー設定
5. Step 5: テストネットで鍵紛失シナリオをシミュレート

**結果**: ユーザーは秘密鍵を紛失しても、Guardian 2/3 の承認で新しい鍵に切り替えられる。

## トラブルシューティング

### 1. UserOperation のバリデーション失敗

**症状**: Bundler が `AA2x` エラーコードを返す

**原因と対策**:
- **AA21 (didn't pay prefund)**: Smart Account に十分な ETH がない。デポジットするか Paymaster を使用する。
- **AA23 (reverted)**: `validateUserOp` が revert した。署名が正しいか、nonce が正しいか確認する。
- **AA24 (signature error)**: 署名の形式が不正。Smart Account の署名検証ロジックと署名方法が一致しているか確認する。
- **AA25 (invalid nonce)**: nonce が不正。`getNonce()` で最新の nonce を取得して使用する。nonce はキー付き（2D nonce）のため、キーの選択も確認する。

### 2. Bundler 接続エラー

**症状**: `eth_sendUserOperation` が接続エラーまたは 4xx を返す

**原因と対策**:
- **Bundler URL の不正**: 正しいチェーン用の Bundler URL を設定しているか確認する。Stackup / Pimlico / Alchemy は チェーンごとにエンドポイントが異なる。
- **EntryPoint バージョンの不一致**: Bundler がサポートする EntryPoint バージョン（v0.6 / v0.7）とスマートアカウントの EntryPoint バージョンを一致させる。
- **API キーの期限切れ**: 有料 Bundler サービスの API キーを確認する。

### 3. Paymaster のデポジット不足

**症状**: `AA31 (paymaster deposit too low)` エラー

**原因と対策**:
- **デポジット残高の確認**: `entryPoint.balanceOf(paymasterAddress)` で残高を確認する。
- **デポジットの追加**: `entryPoint.depositTo(paymasterAddress)` で ETH を追加する。
- **モニタリングの設定**: デポジット残高が閾値を下回った場合にアラートを送信する仕組みを構築する。
- **Rate Limiting**: Paymaster の利用上限（1ユーザーあたり/時間あたり等）を設定して枯渇を防ぐ。

### 4. Smart Account のデプロイが失敗する

**症状**: `initCode` を含む最初の UserOp が失敗する

**原因と対策**:
- **Factory アドレスの不正**: `initCode` の先頭 20 バイトが正しい Factory アドレスか確認する。
- **salt の衝突**: 同じ salt で既にアカウントがデプロイされている。`getAddress()` で既存アカウントの有無を確認する。
- **ガス不足**: `verificationGasLimit` が Factory のデプロイコストを含んでいるか確認する。初回は通常より多いガスが必要。

### 5. バッチトランザクションの部分失敗

**症状**: `executeBatch` の一部操作が revert するが、全体が失敗する

**原因と対策**:
- **アトミック実行**: デフォルトの `executeBatch` はアトミック（1つ失敗で全体失敗）。意図的な動作。
- **try-catch パターン**: 部分的な失敗を許容する場合は、各操作を try-catch でラップした delegate call を使用する。
- **シミュレーション**: `eth_estimateUserOperationGas` で事前に全操作のシミュレーションを行い、失敗する操作を除外する。

## 注意事項

- **EntryPoint のバージョン管理**: ERC-4337 は v0.6 と v0.7 で互換性がない。スマートアカウント・Bundler・Paymaster 全てを同じバージョンに揃える。
- **秘密鍵の管理**: Smart Account のオーナーキーは EOA の秘密鍵。紛失するとアカウントの制御を喪失する（Recovery Module がない場合）。
- **ガスオーバーヘッド**: AA トランザクションは通常の EOA トランザクションよりガスが 20-40% 多い。Paymaster 使用時は dApp がこのコストを負担する。
- **Bundler の中央集権リスク**: 単一の Bundler に依存するとダウンタイムリスクがある。複数 Bundler のフェイルオーバーを設定する。
- **コントラクトの Solidity 実装**（Smart Account / Paymaster の内部ロジック）は `solidity-core` のセキュリティパターンを参照する。
- **フロントエンド統合**は `web3-frontend` スキルの viem / wagmi パターンを参照する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
