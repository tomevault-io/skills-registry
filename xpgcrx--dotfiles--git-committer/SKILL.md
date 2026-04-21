---
name: git-committer
description: Gitのcommitについてのガイドライン。commitを作成する前に使用する。 Use when this capability is needed.
metadata:
  author: xpgcrx
---

# Gitコミット標準

## Conventional Commits

以下のようなConventional Commitsの作法に従ったコミットメッセージを作成すること。

```bash
# フォーマット: <type>(<scope>): <subject>
git commit -m "feat(auth): add JWT token refresh"
git commit -m "fix(api): handle null response correctly"
git commit -m "docs(readme): update installation steps"
git commit -m "perf(db): optimize query performance"
git commit -m "refactor(core): extract validation logic"
```

## コミットその他

以下のような使用ツールに関する文言はコミットメッセージには不要。

```bash
Generated with [Claude Code](https://claude.ai/code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xpgcrx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
