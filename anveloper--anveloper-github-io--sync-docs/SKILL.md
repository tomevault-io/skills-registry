---
name: sync-docs
description: 특정 기간의 커밋을 분석하여 문서에 반영되지 않은 변경사항을 찾고 문서를 업데이트합니다. Use when this capability is needed.
metadata:
  author: anveloper
---

# 커밋 기반 문서 동기화

인자: `$ARGUMENTS`

## 1단계: 기간 파싱 및 커밋 조회

| 인자     | 의미           | git 명령                |
| -------- | -------------- | ----------------------- |
| `today`  | 오늘 커밋      | `--since="00:00"`       |
| `3d`     | 최근 3일       | `--since="3 days ago"`  |
| `1w`     | 최근 1주       | `--since="1 week ago"`  |
| `2w`     | 최근 2주       | `--since="2 weeks ago"` |
| `<hash>` | 특정 커밋 이후 | `<hash>..HEAD`          |
| (없음)   | 오늘 커밋      | `--since="00:00"`       |

```bash
# 예시: 최근 1주 커밋
git log --oneline --since="1 week ago"

# 상세 변경 내용
git log --stat --since="1 week ago"
```

## 2단계: 변경사항 분류

각 커밋을 분석하여 카테고리 분류:

### 문서 필요 여부 판단

| 변경 유형          | 문서 필요 | 대상 문서                    |
| ------------------ | --------- | ---------------------------- |
| 새 페이지 추가     | O         | `docs/design/`               |
| 컴포넌트 구조 변경 | O         | `claude.md` 또는 design 문서 |
| 새 컴포넌트 추가   | O         | 관련 설계 문서               |
| 새 훅/유틸 추가    | O         | 관련 기능 문서               |
| 설정 변경          | O         | `claude.md`                  |
| 패키지 업데이트    | O         | `claude.md` 기술 스택        |
| 버그 수정          | X         | -                            |
| 스타일 변경        | X         | -                            |
| 단순 리팩토링      | △         | 구조 변경 시만               |

## 3단계: 기존 문서 확인

```bash
# 설계 문서 목록
ls -la docs/design/

# 작업 일지 목록
ls -la docs/logs/

# claude.md 확인
cat claude.md
```

각 변경사항이 이미 문서화되어 있는지 확인:

- 문서에 관련 내용이 있는지 Grep으로 검색
- 문서 최종 수정일과 커밋 날짜 비교

## 4단계: 누락된 문서 작성/업데이트

### 기존 문서 수정 시

- 해당 섹션에 내용 추가
- 수정일 명시 (예: `수정: 2026-02-01`)

### 새 문서 작성 시

**설계 문서:**

```
docs/design/YYYYMMDD-<topic>.md
```

**작업 일지:**

```
docs/logs/YYYYMMDD-<title>.md
```

작업 일지 형식:

```markdown
# YYYY-MM-DD 작업 일지: <제목>

## 작업 내용

### feat (새 기능)

- 기능 1 설명
- 기능 2 설명

### fix (버그 수정)

- 수정 내용

### refactor (리팩토링)

- 리팩토링 내용

### docs (문서)

- 문서 변경 내용

## 관련 커밋

- `abc1234` feat: ...
- `def5678` fix: ...

## 다음 작업

- 예정된 작업 목록
```

### claude.md 업데이트 시

- 기술 스택 버전 변경 반영
- 디렉토리 구조 변경 반영
- 새 규칙/패턴 추가

## 5단계: 커밋

문서 변경사항을 기능별로 묶어서 커밋합니다.

### 커밋 규칙

- 타입: `docs`
- 한글로 작성, 첫 글자 소문자, 마침표 없음
- 관련 파일끼리 묶어서 커밋

```bash
git add <변경된 문서 파일들>
git commit -m "$(cat <<'EOF'
docs: <한글 설명>
EOF
)"
```

### 예시

```bash
# CLAUDE.md + 작업 일지를 함께 커밋
git add CLAUDE.md docs/logs/20260213-header-logo-refactor.md
git commit -m "docs: HeaderLogo 분리 작업 일지 및 디렉토리 구조 갱신"
```

## 6단계: 결과 보고

### 분석 요약

```
분석 기간: <시작> ~ <끝>
총 커밋 수: N개
문서 필요 커밋: M개
```

### 업데이트 내역

| 문서               | 변경 유형 | 관련 커밋 |
| ------------------ | --------- | --------- |
| docs/design/xxx.md | 수정      | abc1234   |
| docs/logs/yyy.md   | 신규      | def5678   |

### 미반영 항목 (있을 경우)

- 수동 확인 필요한 항목
- 판단이 애매한 변경사항

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anveloper) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
