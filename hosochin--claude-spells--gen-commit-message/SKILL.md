---
name: gen-commit-message
description: git diff からコミットメッセージを3〜5個生成する Use when this capability is needed.
metadata:
  author: hosochin
---

gen-commit-message/input.md を読み取り、`git diff` コマンドを実行して変更内容を取得し、適切なコミットメッセージを3~5個生成してください。

input.md のフォーマット：
```
言語: ja または en（指定がない場合は ja）
```

要件：
- `git diff` コマンドを実行して変更内容を取得すること
- Conventional Commits 形式に従うこと（feat:, fix:, refactor:, docs:, test:, chore: など）
- 簡潔で変更内容が明確に伝わるメッセージにすること
- 各メッセージについて選定理由を記載すること
- 一番おすすめのメッセージを最初に明示すること
- 指定された言語でコミットメッセージを生成すること（ja: 日本語、en: 英語）

出力フォーマット：
```
【おすすめ】
コミットメッセージ

おすすめ理由: なぜこれが最適かの説明

---

【その他の候補】

1. コミットメッセージ
   理由: この名前を提案した理由

2. コミットメッセージ
   理由: この名前を提案した理由

3. コミットメッセージ
   理由: この名前を提案した理由

（必要に応じて4～5個まで）
```

$ARGUMENTS に `test` が指定されている場合は、gen-commit-message/sample.md の内容を入力として使用してください。

生成されたコミットメッセージを gen-commit-message/output.md に出力してください。

**重要**: 出力する際は、Writeツールではなく、Bashツールで `cat` コマンドとHEREDOC（`<<'EOF'`）を使用して書き込んでください。これにより文字化けを防ぐことができます。

例:
```bash
cat > gen-commit-message/output.md <<'EOF'
（生成した内容）
EOF
```

サンプル入力は gen-commit-message/sample.md を参照してください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hosochin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
