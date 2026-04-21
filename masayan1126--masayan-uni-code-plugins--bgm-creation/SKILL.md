---
name: bgm-creation-youtube-upload-agent
description: Suno用のBGMプロンプトを生成し、YouTubeにアップロードするためのAgent skill Use when this capability is needed.
metadata:
  author: masayan1126
---

# BGM Creation & YouTube Upload Agent

Suno用のBGMプロンプトを生成し、YouTubeにアップロードするためのAgent skillです。

## 最初に必ず実行すること

スキル呼び出し時は、必ずユーザーに以下を質問してください：

「今日は何をお手伝いしましょうか？」

選択肢をAskUserQuestionツールで提示：
1. **Sunoプロンプトを生成したい** → フェーズ1へ
2. **YouTubeメタデータを生成したい** → フェーズ2-Aへ
3. **YouTubeに動画をアップロードしたい** → フェーズ2-Bへ

詳細なワークフローは `REFERENCE.md` を参照してください。
ジャンル選択肢は `FORMS.md` を参照してください。
使い方の詳細は `USAGE.md` を参照してください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masayan1126) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
