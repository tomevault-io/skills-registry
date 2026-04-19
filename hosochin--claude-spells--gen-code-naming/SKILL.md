---
name: gen-code-naming
description: クラス名・メソッド名・変数名などコーディングに関する命名を3〜5個提案する Use when this capability is needed.
metadata:
  author: hosochin
---

gen-code-naming/input.md を読み取り、適切なコード命名（クラス名、メソッド名、変数名）を3~5個生成してください。

要件：
- 言語ごとの命名規則に従うこと（例: JavaScript はキャメルケース、Python はスネークケースなど）
- 命名の目的や内容が明確に伝わる名前にすること
- 各命名候補について選定理由を記載すること
- 一番おすすめの命名を最初に明示すること

出力フォーマット：
```
【おすすめ】
命名候補

おすすめ理由: なぜこれが最適かの説明

---

【その他の候補】

1. 命名候補
   理由: この名前を提案した理由

2. 命名候補
   理由: この名前を提案した理由

3. 命名候補
   理由: この名前を提案した理由

（必要に応じて4～5個まで）
```

$ARGUMENTS に `test` が指定されている場合は、gen-code-naming/sample.md の内容を入力として使用してください。

生成された命名候補を gen-code-naming/output.md に出力してください。

**重要**: 出力する際は、Writeツールではなく、Bashツールで `cat` コマンドとHEREDOC（`<<'EOF'`）を使用して書き込んでください。これにより文字化けを防ぐことができます。

例:
```bash
cat > gen-code-naming/output.md <<'EOF'
（生成した内容）
EOF
```

サンプル入力は gen-code-naming/sample.md を参照してください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hosochin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
