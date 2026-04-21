---
name: read-unresolved-pr-comments
description: GitHub PR에서 아직 대응되지 않은 코멘트를 조회한다. GraphQL API를 사용해 (1) 코드의 특정 라인에 연결된 미해결 Review thread(Resolve 가능)와 (2) 코드 블록을 포함한 Issue comment(대화 탭, Resolve 불가)를 모두 가져와 JSON 형식으로 출력한다. PR 정보(번호, 제목, URL, 상태, 작성자, 리뷰어)도 함께 포함된다. Use when this capability is needed.
metadata:
  author: gaebalai
---

# Read Unresolved PR Comments

## Instructions
아래 명령어를 실행해, 미해결 상태의 Pull Request 리뷰 코멘트를 조회한다.

```
bash scripts/read-unresolved-pr-comments.sh 
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaebalai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
