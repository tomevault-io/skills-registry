---
name: git
description: Git 커밋 자동화. Semantic Commit 규칙으로 타입 분류, 한글 메시지 생성, 이슈 링크 Use when this capability is needed.
metadata:
  author: simgyuhwan
---

# Git 커밋 스킬

## 워크플로우

1. **변경사항 감지**: `git status`
2. **분석**: 파일 → Type, Scope 결정
3. **메시지 생성**: Semantic Commit
4. **커밋**: `git commit`

## Semantic Commit

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types
- `feat`: 새 기능
- `fix`: 버그 수정
- `refactor`: 리팩토링
- `docs`: 문서
- `test`: 테스트
- `chore`: 빌드/설정

상세: `reference/commit-types.md`

## 자동 분석

### Type 결정
```bash
# 새 파일 → feat
git diff --cached --name-status | grep "^A"

# 버그 수정 → fix
git diff --cached | grep -i "fix\|bug"

# 테스트만 → test
git diff --cached --name-only | grep -E "Test\.java$"
```

### Scope 추출
```bash
# src/main/java/seller/SellerService.java → seller
```

## 출력 예시

```
📝 커밋 메시지

feat(seller): 셀러 신청 기능 추가

- SellerService 신규 생성
- 암호화 적용
- 검증 로직 추가

Resolves: #123

✅ 커밋 완료
Commit: a1b2c3d
Files: 5 changed
```

상세: `reference/commit-types.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/simgyuhwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
