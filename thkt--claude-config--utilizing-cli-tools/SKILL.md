---
name: utilizing-cli-tools
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# CLIツールガイド

## 参照

| カテゴリ       | リファレンス                                        |
| -------------- | --------------------------------------------------- |
| バージョン管理 | `${CLAUDE_SKILL_DIR}/references/git-essentials.md`  |
| GitHub         | `${CLAUDE_SKILL_DIR}/references/gh-github-cli.md`   |
| パッケージ管理 | `${CLAUDE_SKILL_DIR}/references/npm-scripts.md`     |
| CHANGELOG      | `${CLAUDE_SKILL_DIR}/references/changelog-tools.md` |

## クイックリファレンス

### Git

| アクション | コマンド                    |
| ---------- | --------------------------- |
| ステータス | `git status --short`        |
| Diff       | `git diff --staged`         |
| ブランチ   | `git branch --show-current` |
| ログ       | `git log --oneline -10`     |

### HEREDOCコミット

```bash
git commit -m "$(cat <<'EOF'
feat(auth): OAuth認証を追加
EOF
)"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
