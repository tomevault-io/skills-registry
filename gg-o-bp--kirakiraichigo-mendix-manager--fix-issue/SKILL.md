---
name: fix-issue
description: GitHub 이슈를 분석하고 수정 Use when this capability is needed.
metadata:
  author: gg-o-bp
---

GitHub 이슈 #$ARGUMENTS를 수정한다.

1. `gh issue view $ARGUMENTS`로 이슈 내용 확인
2. 관련 코드를 탐색하고 근본 원인 파악
3. 수정 구현
4. 테스트 작성 또는 기존 테스트 실행으로 검증
   - Rust: `cd src-tauri && cargo test`
5. `cd src-tauri && cargo clippy` 린트 확인
6. 수정 내용을 설명하는 커밋 생성

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gg-o-bp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
