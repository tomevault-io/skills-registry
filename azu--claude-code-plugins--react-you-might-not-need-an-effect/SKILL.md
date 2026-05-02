---
name: react-you-might-not-need-an-effect
description: Guide for writing React code without unnecessary useEffect. Use when writing or editing React components. Avoid anti-patterns like derived state, chained state updates, and event handlers in effects. Use when this capability is needed.
metadata:
  author: azu
---

# React: You Might Not Need an Effect

React公式ドキュメント「You Might Not Need an Effect」に基づき、不要なuseEffectを書かないようにする。

## Instructions

Reactコンポーネントを書く・編集する際、useEffectを使う前に以下のパターンに該当しないか確認する。該当する場合はuseEffectを使わない代替案を採用する。

### 検出すべき12のアンチパターン

詳細は [PATTERNS.md](PATTERNS.md) を参照。

#### React公式ドキュメントのパターン

| パターン | 問題 | 解決策 |
|---------|------|--------|
| 導出状態 | propsやstateから計算可能な値をstateで管理 | レンダリング時に計算 |
| 高コスト計算 | useEffectでキャッシュ | useMemoを使用 |
| propsでstate全体リセット | useEffectでリセット | keyを使用 |
| props変更で一部state調整 | useEffectで調整 | レンダリング中に調整 |
| イベントハンドラ処理 | useEffectでAPIコール | イベントハンドラで直接処理 |
| ロジック共有 | useEffectで共通処理 | 関数を抽出して共有 |
| 状態の連鎖更新 | useEffectのチェーン | イベントハンドラで一括更新 |

#### ESLintプラグイン追加パターン

| パターン | 問題 | 解決策 |
|---------|------|--------|
| 内部stateを親に渡す | useEffectでstateを親コールバックに渡す | stateを親にリフトアップ |
| 外部データを親に渡す | useEffectでフェッチデータを親に渡す | 親でデータ取得 |
| refを親に渡す | useEffectでref.currentを親に渡す | forwardRefを使用 |
| state初期化 | useEffect内でリテラル値でstate設定 | useStateの初期値で指定 |
| 空のuseEffect | 中身が空のuseEffect | 削除する |

## References

- React公式: https://react.dev/learn/you-might-not-need-an-effect
- ESLintプラグイン: https://github.com/NickvanDyke/eslint-plugin-react-you-might-not-need-an-effect

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/azu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
