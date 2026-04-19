---
name: code-review
description: コードレビューチェックリスト Use when this capability is needed.
metadata:
  author: shadowlink0122
---

# コードレビューチェック

コード変更のレビュー時に確認すべき項目のスキルです。

## 確認項目

### 1. コード品質
- [ ] コードスタイルがプロジェクト規約に準拠
- [ ] 適切なコメントと命名
- [ ] マジックナンバーなし
- [ ] 重複コードなし

### 2. 安全性
- [ ] nullチェック適切
- [ ] 境界値チェック
- [ ] リソース解放適切
- [ ] エラーハンドリング

### 3. パフォーマンス
- [ ] 不要なコピーなし
- [ ] ループ内の重い処理なし
- [ ] 適切なデータ構造

### 4. テスト
- [ ] 新機能にテスト追加
- [ ] エッジケースカバー
- [ ] 既存テストへの影響なし

### 5. ドキュメント
- [ ] 公開APIにドキュメント
- [ ] 複雑なロジックに説明
- [ ] READMEに変更反映
- [ ] リリースノートに記載
- [ ] チュートリアル（ja/en）追加

## レビューコマンド
```bash
# 変更差分確認
git diff HEAD~1

# 変更ファイル一覧
git diff --name-only HEAD~1
```

## ドキュメント更新チェックリスト

新機能追加時には以下を確認：

- [ ] `docs/releases/v*.md` - リリースノートに記載
- [ ] `docs/tutorials/ja/` - 日本語チュートリアル追加
- [ ] `docs/tutorials/en/` - 英語チュートリアル追加
- [ ] `README.md` - 必要であれば更新
- [ ] `docs/FEATURES.md` - 機能一覧を更新

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shadowlink0122) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
