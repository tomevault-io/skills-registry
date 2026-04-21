---
name: create-git-worktree
description: git worktree를 활용해 분리된 작업 환경을 자동으로 구축한다. 기본 브랜치에서 최신 코드를 가져와 `.git-worktrees/` 디렉터리에 새로운 worktree를 생성하고, `.env`, Serena memories, npm 의존성을 자동으로 설정한다. 브랜치명에 포함된 `/`는 자동으로 `-`로 변환된다. 이미 존재하는 worktree가 있을 경우 재사용한다. Use when this capability is needed.
metadata:
  author: gaebalai
---

# Create Git Worktree and Setup Environment

## Instructions

아래 명령어를 실행해 git worktree를 생성하고 환경 설정을 수행한다.  
인자에는 git worktree로 만들 브랜치 이름을 지정한다.

```
bash scripts/create-worktree.sh [브랜치명]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaebalai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
