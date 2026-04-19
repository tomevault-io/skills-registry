---
name: sakura-mml-music-theory
description: >- Use when this capability is needed.
metadata:
  author: karupanerura
---

# Sakura MML Music Theory — 音楽理論に基づく作曲支援

## Overview

本スキルは、音楽理論の知識を Sakura MML 作曲に適用するための包括的なガイドである。
音程計算、スケール/モード構成、コード分析、コード進行設計、機能和声、
ボイスリーディング、転調といった理論的概念を、具体的な MML コマンドに翻訳する
プロセスを体系化する。

**重要な原則：推測の禁止 (No Guessing Rule)**

音程計算、コード構成音の特定、スケールの構成音列挙など、
決定論的に確定できる事項について、確率的推論や記憶に頼ってはならない。
必ず本スキルの知識体系に基づいて論理的に導出すること。
特に異名同音（Enharmonic）の区別は文脈に依存するため、
調性（Key）を常に意識して判断すること。

## Prerequisite Skills

本スキルは `sakura-mml-composition` スキルの知識を前提とする。
MML の基本構文、コマンド体系については `sakura-mml-composition` を参照すること。
楽器別のリアリズム技法については `sakura-mml-instrument-makeup` を参照すること。

## Reference Guide — 参照ファイルの選択基準

以下の基準で必要な参照ファイルを読み込むこと。
不要なファイルは読み込まず、コンテキスト効率を維持する。

### 基礎理論

- 音程の計算・判定・転回 →
  [references/intervals.md](references/intervals.md)
- スケール・モードの構成と特徴 →
  [references/scales-and-modes.md](references/scales-and-modes.md)
- コードの構成・種類・テンション →
  [references/chords.md](references/chords.md)

### 和声と進行

- ダイアトニックコード・機能和声・代理コード →
  [references/functional-harmony.md](references/functional-harmony.md)
- コード進行パターンとケーデンス →
  [references/chord-progressions.md](references/chord-progressions.md)
- ボイスリーディングと対位法的制約 →
  [references/voice-leading.md](references/voice-leading.md)

### 応用理論

- 転調・借用和音・モーダルインターチェンジ →
  [references/modulation.md](references/modulation.md)
- メロディ構築の理論的基盤 →
  [references/melody-construction.md](references/melody-construction.md)

### 実践クイックリファレンス

- 全キーのダイアトニックコード一覧表 →
  [references/diatonic-chord-table.md](references/diatonic-chord-table.md)

## Core Concepts — MML への翻訳原則

### 音名と MML ノート名の対応

| 日本語 | 英語 | MML | MIDI例 (o5) |
|--------|------|-----|-------------|
| ド | C | `c` | 72 |
| レ | D | `d` | 74 |
| ミ | E | `e` | 76 |
| ファ | F | `f` | 77 |
| ソ | G | `g` | 79 |
| ラ | A | `a` | 81 |
| シ | B | `b` | 83 |

- シャープ: `c+` or `c#` (C#)
- フラット: `d-` (Db)
- ダブルシャープ: `c++` (C##)
- ダブルフラット: `d--` (Dbb)
- オクターブ指定: `o4` = 中央ド付近、`o5` = 1オクターブ上

### 調号の MML 設定

Sakura MML の `KeyFlag` で調号を設定できる。

```
// Key of G Major (F#)
KeyFlag+(f)

// Key of D Major (F#, C#)
KeyFlag+(f)
KeyFlag+(c)

// Key of F Major (Bb)
KeyFlag-(b)

// Key of Eb Major (Bb, Eb, Ab)
KeyFlag-(b)
KeyFlag-(e)
KeyFlag-(a)

// Reset to C Major / A Minor
KeyFlag=(cdefgab)
```

### コードの MML 表現

3つの主要な方法でコードを記述する:

```
// 方法1: コード記法（最も簡潔）
'ceg'4           // C major triad, quarter note

// 方法2: ゼロ長ノート（柔軟性が高い）
c0e0g4           // 同上

// 方法3: Sub による重ね合わせ（ボイシング制御に最適）
Sub{ o3 c4 } Sub{ o4 e4 } Sub{ o4 g4 } o5 c4
```

ボイスリーディングを意識した進行では方法3を推奨する。
各声部の動きを独立して制御できるため。

### コード進行の典型的な MML 構造

```
// I - V - vi - IV (C major) のバッキング例
Track(2) Channel(2) Voice(1) // Piano backing
v80 q75 o4 l4

// I: C major
Sub{ c4 } Sub{ e4 } g4
// V: G major
Sub{ "b4 } Sub{ d4 } g4
// vi: A minor
Sub{ c4 } Sub{ e4 } a4
// IV: F major
Sub{ c4 } Sub{ f4 } a4
```

## 分析ワークフロー

ユーザーから音楽理論に関する質問を受けた際は、以下のフローに従う:

### 1. コード特定タスク

1. 構成音を列挙する
2. ルート（根音）を推定する
3. 3度・5度・7度の音程を計算する
4. コード品質（Major/Minor/Dim/Aug/7th等）を判定する
5. 転回形を特定する
6. 調性内での機能（T/SD/D）を判定する

### 2. スケール/モード生成タスク

1. ルート音を確認する
2. スケール種類の音程パターンを参照する
3. 全構成音を列挙する
4. MML の `KeyFlag` 設定を生成する
5. MML ノート列として出力する

### 3. コード進行設計タスク

1. キー（調）を確定する
2. ダイアトニックコード表を参照する
3. 機能（T→SD→D→T等）の流れを設計する
4. ボイスリーディングを検討する
5. MML コードシーケンスとして出力する

### 4. メロディ構築タスク

1. キーとスケールを確定する
2. コード進行を確認する（あれば）
3. コードトーンを強拍に配置する
4. 経過音・刺繍音で装飾する
5. リズムパターンを適用する
6. 音域の起伏（コンター）を設計する

## Key Caveats — 注意事項

1. **異名同音の区別**: C# と Db は同じ鍵盤だが理論上は異なる。
   調性の文脈で正しい表記を選択すること。
2. **MML の `Key()` コマンドは移調**: `Key(n)` は全ノートを n 半音移調する。
   調号設定には `KeyFlag` を使用すること。
3. **オクターブ境界**: MML の `o` 指定は `c` から始まる。
   `o4 b` の次の `c` は `o5 c` であることに注意。
4. **MIDI チャンネル 10**: ドラムチャンネルでは音高がパーカッション音色にマッピング
   されるため、音楽理論的な音程計算は適用されない。
5. **テンション/アヴォイドノート**: コードトーンと半音でぶつかるノートは
   アヴォイドノートとなり得る。配置に注意すること。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karupanerura) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
