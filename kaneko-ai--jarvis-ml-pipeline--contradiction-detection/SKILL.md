---
name: contradiction-detection
description: 研究結果の矛盾を分類し、根拠と影響範囲を整理する。 Use when this capability is needed.
metadata:
  author: kaneko-ai
---
# Contradiction Detection

このスキルは、複数の研究結果に含まれる矛盾を検出し、以下のタイプに分類します。

## 矛盾タイプ

- **Direct**: 同一条件で真逆の結論が述べられている
- **Quantitative**: 方向性は同じだが効果量や数値が一致しない
- **Temporal**: 時系列や観測時点の違いにより結論が変化している

## 実施手順

1. 引用文の主張と条件（対象、期間、アウトカム）を抽出する
2. 反対の結果を示す研究と比較する
3. 矛盾タイプを分類し、原因（手法差、サンプル差）を記録する

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaneko-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
