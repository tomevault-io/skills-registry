---
name: ty-pro
description: Python型安全性をtyで強化。型アノテーション追加、型エラー修正、境界バリデーション実装時に使用。 Use when this capability is needed.
metadata:
  author: yunosukeyoshino
---

# ty-pro: Python Type Safety

tyを使ったPython型チェックと型安全なコード設計。

## 適用場面

- 型アノテーションの追加・改善
- ty型エラーの修正
- API境界でのバリデーション実装
- Any/cast/ignore の使用判断

---

## 🔄 型エラー修正ワークフロー

```
型エラー修正進捗:
- [ ] Step 1: ty check実行でエラー特定
- [ ] Step 2: エラー分類（真の問題 vs 抑制候補）
- [ ] Step 3: 修正実装
- [ ] Step 4: ty check再実行で確認
```

**Step 1: エラー特定**
```bash
uv run ty check src/
```

**Step 2: エラー分類**
- 真の型エラー → 修正
- 外部ライブラリ起因 → `# ty: ignore[rule]`で抑制
- 境界入力 → バリデーション追加

**Step 3-4: 修正→確認のループ**

---

## コマンド

```bash
uv run ty check src/           # 型チェック
uv run ty check path/to/file.py  # 単一ファイル
```

---

## エラー抑制ポリシー

### 優先順位

1. **修正**（推奨）: 型を正しく書く
2. **ナローイング**: `isinstance`、`TypeGuard`で絞り込み
3. **抑制**: 外部要因でやむを得ない場合のみ

### 抑制コメント形式

```python
# ✅ 推奨: ルール指定
result = func()  # ty: ignore[invalid-assignment]

# ✅ 複数ルール
value = data["key"]  # ty: ignore[possibly-unbound, invalid-assignment]

# ⚠️ 最終手段: 全抑制（サードパーティ起因のみ）
result = untyped_lib.call()  # type: ignore
```

### 関数単位の抑制

```python
from typing import no_type_check

@no_type_check
def legacy_code():
    # この関数内は型チェックされない
    pass
```

---

## Any/cast使用ポリシー

### Any

- **禁止**: コア・ドメインモジュール
- **許可**: 外部境界のみ（API入力、未型付きライブラリ）
- **必須**: 即座にナローイングして精密型へ変換

```python
# ❌ Anyを漏らす
def process(data: Any) -> Any: ...

# ✅ 境界で受けて即変換
def process(data: object) -> Result:
    validated = parse_input(data)  # object → 精密型
    return handle(validated)
```

### cast

- 使用前に「なぜnarrowing不可か」を説明できること
- 理由コメント必須

```python
# ✅ 理由付きcast
# SDK returns untyped but we know it's always int
count = cast(int, sdk.get_count())
```

---

## 境界バリデーションパターン

### JSON入力

```python
from typing import TypedDict

class UserInput(TypedDict):
    name: str
    age: int

def validate_user(data: object) -> UserInput:
    if not isinstance(data, dict):
        raise ValueError("Expected dict")
    if "name" not in data or "age" not in data:
        raise ValueError("Missing required fields")
    return {"name": str(data["name"]), "age": int(data["age"])}
```

### TypeGuard

```python
from typing import TypeGuard

def is_string_list(val: object) -> TypeGuard[list[str]]:
    return isinstance(val, list) and all(isinstance(x, str) for x in val)
```

---

## モダン型機能（3.11+）

| 機能 | 用途 |
|------|------|
| `Protocol` | 構造的型付け（インターフェース） |
| `TypedDict` | JSON/dict契約 |
| `Literal` | 判別ユニオン |
| `NewType` | ドメインID（UserId等） |
| `Self` | Fluent API |
| `TypeGuard` | カスタムナローイング |

---

## Anti-Patterns

- ❌ `# ty: ignore` を理由なく追加
- ❌ Anyをコアロジックに漏らす
- ❌ castを検証なしで使用
- ❌ 全ファイル抑制（`# type: ignore`をファイル先頭に）
- ❌ 無効なルール名での抑制（typoサプレッション）

---

## 設定

### pyproject.toml

```toml
[tool.ty.rules]
# エラーレベル調整
index-out-of-bounds = "warn"
unused-ignore-comment = "error"  # 不要な抑制を検出
```

### 設定優先順位

1. コマンドラインオプション
2. `ty.toml`（プロジェクト）
3. `pyproject.toml` の `[tool.ty]`
4. ユーザー設定（~/.config/ty/）

---

## References

- [REFERENCE.md](REFERENCE.md) - tyルール一覧
- [ty公式ドキュメント](https://docs.astral.sh/ty/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yunosukeyoshino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
