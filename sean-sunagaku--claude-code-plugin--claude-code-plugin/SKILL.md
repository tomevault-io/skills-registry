---
name: simulator-screenshot-crop
description: > Use when this capability is needed.
metadata:
  author: sean-sunagaku
---

# Simulator Screenshot Cropper

シミュレーターのスクリーンショットから macOS ウィンドウのタイトルバーを自動除去する。

## 仕組み

シミュレーターのスクショは alpha チャンネル付き PNG。タイトルバー部分は不透明で、
デバイスベゼルとの境界から透明になる。画像中央列を走査して最初の alpha=0 ピクセルを
見つけることでバーの高さを自動特定する。

## 前提条件

Python 3 + Pillow が必要。なければ `pip install Pillow` でインストール。

## 使い方

```bash
# 基本: 同じディレクトリに _cropped サフィックスで保存
python scripts/crop_chrome.py ~/Desktop/Screenshot1.png ~/Desktop/Screenshot2.png

# 複数ファイル一括
python scripts/crop_chrome.py ~/Desktop/*.png

# 出力先ディレクトリを指定
python scripts/crop_chrome.py ~/Desktop/*.png -o ~/Desktop/cropped/

# サフィックスをカスタマイズ
python scripts/crop_chrome.py ~/Desktop/*.png -s _clean

# 元ファイルを上書き
python scripts/crop_chrome.py ~/Desktop/*.png --overwrite
```

## オプション

| フラグ | 説明 | デフォルト |
|--------|------|-----------|
| `-o`, `--output-dir` | 出力先ディレクトリ | 元ファイルと同じ場所 |
| `-s`, `--suffix` | 出力ファイル名のサフィックス | `_cropped` |
| `--overwrite` | サフィックスなしで上書き | off |

## 対応解像度

alpha チャンネル方式で自動検出するため、解像度に依存しない:

| 解像度 | 画像幅の目安 | chrome bar の高さ |
|--------|-------------|-------------------|
| 1x | ~434px | ~52px |
| 2x | ~868px | ~104px |
| 3x | ~1302px | ~156px |

## 注意事項

- 入力画像に alpha チャンネルがない場合は自動で RGBA に変換するが、
  chrome bar との境界が検出できない可能性がある
- Android エミュレーターでも同様の構造であれば動作する

---
> Source: [sean-sunagaku/claude-code-plugin](https://github.com/sean-sunagaku/claude-code-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
