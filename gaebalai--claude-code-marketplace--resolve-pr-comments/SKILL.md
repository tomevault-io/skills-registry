---
name: resolve-pr-comments
description: GitHub PR의 미해결 Review thread를 일괄 Resolve한다. GraphQL API를 사용해 미해결 Review thread(코드 특정 라인에 대한 코멘트)를 조회한 뒤, resolveReviewThread mutation으로 모두 자동 Resolve한다. Issue comment(대화 탭)는 원래 Resolve 기능이 없으므로 대상에서 제외된다. 각 스레드의 Resolve 결과를 출력한다. Use when this capability is needed.
metadata:
  author: gaebalai
---

# Resolve PR Comments

## Instructions

아래 명령어를 실행해 Resolve되지 않은 리뷰 코멘트를 Resolve한다.

```
bash scripts/resolve-pr-comments.sh
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaebalai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
