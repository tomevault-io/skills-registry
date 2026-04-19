---
name: sakura-mml-instrument-makeup
description: >- Use when this capability is needed.
metadata:
  author: karupanerura
---

# Sakura MML Instrument Makeup — 楽器別リアリズム技法

## Overview

本スキルは、Sakura MML で MIDI 楽器を「人間が演奏したように」聴かせるための
具体的なパラメータ設計手法をまとめたものである。楽器の物理的挙動・演奏者の身体的制約・
音響心理学的効果を、MML の `v`（Velocity）、`q`（Gate time）、`t`（Timing offset）、
CC コマンド（`M`, `EP`, `PitchBend` 等）、および先行指定（`.onNote`, `.onTime` 等）へ
翻訳するプロセスを体系化する。

## Reference Guide

- ドラム・パーカッションのリアリズム →
  [references/drums.md](references/drums.md)
- ベースラインの物理シミュレーション →
  [references/bass.md](references/bass.md)
- ギターのストラミングと表情付け →
  [references/guitar.md](references/guitar.md)
- ピアノのペダリングとボイシング →
  [references/piano.md](references/piano.md)
- オーケストラ楽器のダイナミクス制御 →
  [references/orchestra.md](references/orchestra.md)
- ヒューマナイズとワークフロー全般 →
  [references/humanize.md](references/humanize.md)

## Core Principles — 不気味の谷を越える3つの軸

MML で生楽器のリアリズムを追求する際、以下の 3 軸を常に意識する。

### 1. Velocity（強弱） — 力学の再現

人間は完全に均一な力で楽器を演奏できない。すべての楽器パートにおいて、
ベロシティに「意図的な揺らぎ」と「階層構造」を持たせることが必須である。

**実務上の初期値: `v.Random(3)`**（多くの編成で扱いやすい）

```
// NG: 機械的な均一ベロシティ
v100 l8 cccc cccc

// OK: v.Random(3) + 構造的なアクセント
v.Random(3);
v.onNote(100, 75, 90, 70) l8 cccc cccc
v100  // cancel
```

### 2. Timing（タイミング） — 時間軸の揺らぎ

完全にグリッド上に配置された音は「打ち込み臭さ」の最大の原因。
`t`（Timing offset）と `t.Random()` を用いて微細なズレを導入する。

```
// 微細なランダムタイミング（±3 ticks）
t.Random(3)
l8 cccc cccc
t0 t.Random(0)  // cancel
```

### 3. Gate time（音の長さ） — アーティキュレーションの核

`q`（Gate time）は音の「切れ方」を決定し、スタッカート〜レガートまでの
アーティキュレーション空間を表現する。楽器・奏法ごとに適切な値が異なる。

| アーティキュレーション | q 値目安 | 用途例 |
|---|---|---|
| ミュート（カッティング） | q25 | ギターの弱アップストローク |
| スタッカート | 40–55 | 軽快な刻み、撥弦のカッティング |
| ノーマル | 70–90 | 通常の演奏（q88–q90 が頻出） |
| テヌート | 90–98 | 歌い込むフレーズ（q98 が標準） |
| レガート | 99–100 | ストローク実音（q99）、管楽器持続 |

## 汎用ユーティリティ関数

以下の関数は本スキル向けに整理した汎用テンプレートである。
楽器を問わず利用可能。詳細は [references/humanize.md](references/humanize.md) 参照。

```
// グレイスノート（しゃくり上げ）
Function Gb(nt) { Sub{ p%.onTime=-2048,0,(nt); } }

// ディレイドビブラート（漸増型）
Function Vb(nt) { Sub{ y1.onTime=0,32,(nt); } }

// エクスプレッションフェード
Function Mt(nt) { Sub{ l%24 r4 y11.onTime=127,43,(nt)-4;
    r(nt)-4 y11=127; l%24 } }

// ユニゾン・オクターブダブリング
Function Unison(Str S, Int Value){
    Sub{ Key(Value); S; Key(0); } S;
}
```

## Quick Recipe — 楽器別 v / q / t 初期設定

以下は各楽器パートの出発点となるパラメータ設定例。
本リポジトリ内の検証フレーズを基にした、再利用しやすい汎用形である。
詳細は各楽器の参照ファイルを参照のこと。

### ドラム（Channel 10）— 多層 Rhythm 方式

```
Track(10) Channel(10)
// マクロ定義
$k{n36,} $K{n35,} $s{n38,} $S{n40,}
$h{n42,} $i{n44,} $H{n46,} $T{n39,}

// 多層 Rhythm ブロック（各打楽器を独立レイヤーに）
l%24 v.Random(3);
Rhythm{
    (v120 Sub){ krrr rrrr krrk rrrr  krrr rrrr krrk rrrr }
    (v110 Sub){ rrrr srrr rrrr srrr  rrrr srrr rrrr srrr }
    (v110 Sub){ Hrrh irrh hrrH irrh  Hrrh irrh hrrH irrh }
    (v110 Sub){ rrrr Trrr rrrr Trrr  rrrr Trrr rrrr Trrr }
}
```

### ベース

```
Track(2) Channel(2) Voice(34)  // FingerBass
o3 q90 l%24 v120 v.Random(3);
// メロディアスなライン（^でタイ、r12で隙間）
c^^r>g<rce^ed^c^<b^>
```

### ギター（コード関数方式）

```
Track(3) Channel(3) Voice(28)  // CleanGuitar
// コード関数の定義
Function G9(len,cut) {
    If(cut==1){ ' t0v-20g  t1v+10b >t2v+5d  t3v+5a<'(len)
    }Else{      '>t0v-20a  t1v+10d <t2v+5b  t3v+5g '(len) }
}
// ストロークパターン
o4 l%24 v.Random(3);
v120 q99 G9(48,1);  v100 G9(24,0);  v110 G9(24,1);
v80  q25 G9(24,0);  v110 q99 G9(24,1);
```

### キーボード / EP（Sub{} 多声方式）

```
Track(1) Channel(1) Voice(6)  // EP
// Sub{} で左手・右手を分離
Sub{ o4 q98 l%24 v90 v.Random(3);
    'ceg'^^'ceg'^ 'fac'^^'fac'^
}
o5 q98 l%24 v110 v.Random(3);
e^^red^er
```

### ストリングス

```
Track(4) Channel(4) Voice(49)  // Strings1
// CC1(Modulation) + CC11(Expression) の二層制御
M.onTime(30, 100, !1)    // 1小節かけてクレッシェンド
EP(110)
q98 o5 l1 c d e f
M(0) EP(127)              // cancel

// Unison() でオクターブ厚みを出す
Unison({ o5 q95 l%48 v110 v.Random(3);
    r192b>cd<b r192>agf+e
},12)
```

## Prerequisite Skills

本スキルは `sakura-mml-composition` スキルの知識を前提とする。
MML の基本構文、先行指定（`.onNote`, `.onTime` 等）、リズムモード、
CC コマンドの詳細については `sakura-mml-composition` を参照すること。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karupanerura) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
