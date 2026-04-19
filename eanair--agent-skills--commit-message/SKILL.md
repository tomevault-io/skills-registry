---
name: commit-message
description: Use when working with a skill used when users request to write a commit message.
metadata:
  author: eanair
---

# Commit Message Writing

Check the changes in staged files, write only the commit message, and provide it to the user. The user must execute the Commit and Push commands themselves.

## Basic Usage

```bash
# Checking staging status
git status

# Staged changes check
git diff --staged

# Check the last 5 commit messages for style reference
git log --oneline -5
```

## Commit Message Example

### Example 1 (Low Complexity)

```text
API 라우팅 및 인증 설정 수정
  - nginx 백엔드 프록시 포트 수정 (13000 → 3000)
  - 프론트엔드 API 경로 prefix 수정 (/api → /api/v1)
  - 상품 조회 API 공개 접근 설정 (@Public 데코레이터)
```

### Example 2 (Medium Complexity)

```text
CLAUDE.md 프로덕션 환경 정보 업데이트
  - Docker 명령어 수정 (ENV=production)
  - Production Environment 섹션 추가
    - SSL 인증서 경로, NAS 스토리지, 네트워크 설정
    - Toss Payments 결제 시스템 정보
```

### Example 3 (High Complexity)

```text
1. LoginButton 수정 (frontend/src/features/auth/ui/LoginButton.tsx)
  - 로그인 상태에 따라 조건부 렌더링
  - 비로그인: 기존 로그인 버튼
  - 로그인: 프로필 아이콘 + 드롭다운 메뉴 (마이페이지, 주문내역, 로그아웃)
2. 마이페이지 생성
  - frontend/src/routes/_layout/mypage.tsx - 메인 라우트
  - frontend/src/routes/_layout/mypage/index.tsx - 인덱스 라우트
  - frontend/src/routes/_layout/mypage/orders.tsx - 주문내역 라우트
  - frontend/src/routes/_layout/mypage/profile.tsx - 프로필 수정 라우트
  - frontend/src/pages/mypage/ui/mypage.page.tsx - 마이페이지 컴포넌트
  - frontend/src/pages/mypage/ui/profile-edit.page.tsx - 프로필 수정 컴포넌트
3. 백엔드 프로필 이미지 업로드 API
  - backend/src/users/services/profile-upload.service.ts - 업로드 서비스
  - backend/src/users/controllers/users.controller.ts - 엔드포인트 추가
      - POST /api/v1/users/profile-image - 업로드
    - DELETE /api/v1/users/profile-image - 삭제
  - backend/src/users/users.module.ts - 모듈 등록
4. 프로필 이미지 업로드 컴포넌트
  - frontend/src/shared/ui/profile-image-upload.tsx
  - 이미지 선택, 미리보기, 업로드, 삭제 기능
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eanair) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
