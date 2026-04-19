---
name: design-draft-boundary
description: > Use when this capability is needed.
metadata:
  author: sososha
---

# design-draft-boundary

## 目的
「設計相談への回答」で勝手にコードを書く事故を防ぐ。
DesignDraft の成果物は docs/*.md のみ。

---

## 適用条件
- Task Type = DesignDraft のとき自動適用
- GPT から Q&A が来たとき
- 「〜はどうすべきか」系の質問への回答

---

## Output format

```markdown
# Design Draft: <title>
- Date:
- Status: DRAFT（正本ではない）
- Type: DesignDraft

## 目的
（1〜3行）

## 検討内容
（案を文書で記述）

## 仮定した値（もしあれば）
| 項目 | 値 | 根拠 | 状態 |
|-----|---|-----|------|
| ... | ... | 仮定/未確定 | DRAFT |

## 次のステップ
- [ ] レビュー（Codex/Human）
- [ ] Permit 取得
- [ ] Implementation
```

---

## STOP 条件（必ず守る）

以下のいずれかに該当したら **STOP**：

1. **構造体を書きたくなった**
   - `struct` / `enum` を書きたい → STOP
   - mod.rs を作りたい → STOP

2. **正本として数値を固定したくなった**
   - コードに 120mm / 910mm 等を埋め込みたい → STOP
   - **仮定として docs に書くのは OK**（必ず DRAFT 明記）

3. **正本っぽいものを作りたくなった**
   - Store / Model / Repository 等 → STOP
   - canonical-source-guard を実行

4. **不明点が出た**
   - 推測せず Human に質問（最大3問 A/B/C）

---

## STOP 出力テンプレ

```markdown
## STOP

### Reason（1行）
（例：struct を書きたくなった）

### What I tried
- ...

### What I need from Human（最大3問 A/B/C）
A) ...
B) ...
C) ...

### Next action
- [ ] Human 回答待ち
- [ ] Permit 取得後 Implementation
```

---

## STOP 後のフロー

```
STOP → 質問（最大3問）→ Human 回答 → 
  → 文書に反映 → レビュー → Permit → Implementation
```

---

## 次に使う Skill
- codex-review-request（レビュー依頼）
- canonical-source-guard（正本二重化チェック）

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sososha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
