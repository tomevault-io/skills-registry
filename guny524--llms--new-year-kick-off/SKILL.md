---
name: new-year-kick-off
description: | Use when this capability is needed.
metadata:
  author: guny524
---

# New Year Kick-off PPT Generator

## Quick Start

1. pptx skill 활성화: `Skill(document-skills:pptx)`
2. 작업 폴더 생성 및 초기화:
```bash
mkdir ppt_workspace && cd ppt_workspace
git init
npm init -y
npm install pptxgenjs playwright sharp
```

3. 필요 파일 복사:
   - `html2pptx.js` (scripts/ 에서 복사)
   - `create_ppt.js` (scripts/ 에서 복사)

4. todo.txt 입력 → 슬라이드 HTML 생성 → PPT 변환

## Workflow

### 1. 입력 수집

사용자로부터 다음 정보 수집:
- **todo.txt**: 발표 내용 (See [references/input_format.md](references/input_format.md))
- **user_email**: Git 통계용 이메일 (default: `mingi.jo@zereone.ai` or `guny524@gmail.com`)
- **year**: 대상 연도 (default: 현재 연도)
- **youtube_links**: 공유할 유튜브 링크 목록 (optional)
- **gpt_analysis**: GPT web에서 복사한 장단점 분석 (optional)

### 2. 슬라이드 구조

See:
- [references/company_template.md](references/company_template.md) - 회사 표준 목차
- [references/slide_template.md](references/slide_template.md) - HTML 슬라이드 템플릿
- [references/writing_guidelines.md](references/writing_guidelines.md) - PPT 작성 주의사항

표준 구조:
1. 표지 (제목, 이름, 날짜)
2. 2025 계획 (연초 목표)
3-6. 프로젝트별 ACTION (프로젝트명, MR/commit 통계, 주요 구현, 어려웠던 점)
7. 통계 (월별 MR 추이, 프로젝트별 비율)
8. 강점/약점 분석 + 개선 루틴
9. 잘하는 것 부스팅 계획
10. 2026 ACTION 계획
11. 프로젝트 외 공유 (유튜브 썸네일 + 링크)
12. 마무리 (Fin.)

### 3. Git 통계 수집

Run `scripts/analyze_git_stats.py`:
```bash
python3 scripts/analyze_git_stats.py --email "user@email.com" --year 2025 --repos "/path/to/repo1,/path/to/repo2"
```

Output: 프로젝트별 MR/commit 수, 월별 추이 데이터

GitLab/GitHub 사용 시:
```bash
glab mr list --author=@me --state=merged
gh pr list --author=@me --state=merged
```

### 4. 장단점 분석

Run `scripts/analyze_strengths.py`:
```bash
python3 scripts/analyze_strengths.py --claude-history ~/.claude --codex-history ~/.codex
```

분석 소스:
1. **codex exec**: `codex exec "분석해줘: [대화기록 요약]"`
2. **GPT web 붙여넣기**: 사용자가 제공
3. **.claude/.codex 대화기록**: 로컬 파일 분석

Output 구조:
- 잘하는 것 (내 생각)
- 잘하는 것 (LLM 분석)
- 약점 (LLM 쓴소리)
- 개선 루틴

### 5. 유튜브 썸네일 처리

Run `scripts/fetch_youtube_thumbnail.py`:
```bash
# 단순 URL 목록
python3 scripts/fetch_youtube_thumbnail.py --urls "url1,url2" --output ./images

# 채널별 그룹핑
python3 scripts/fetch_youtube_thumbnail.py --urls "채널1:url1,url2;채널2:url3" --output ./images
```

Options:
- `--urls`: URL 목록 (채널별 그룹핑: `채널명:url1,url2;채널명2:url3`)
- `--output`: 출력 디렉토리 (default: ./images)
- `--json`: JSON 형식으로 출력

출력:
- 채널별로 그룹핑된 HTML 스니펫
- 썸네일 이미지: `yt_{VIDEO_ID}.jpg`
- 이미지에 하이퍼링크 자동 연결

### 6. HTML 슬라이드 생성

각 슬라이드를 HTML로 생성:
- 크기: 720pt x 405pt (16:9)
- 헤더: #1C2833 배경, 흰색 텍스트
- 액센트: #5DADE2
- 링크: #3498DB (inline style 필수!)

```html
<a href="https://..." style="color: #3498DB;">링크텍스트</a>
```

### 7. PPT 변환

```bash
node create_ppt.js --author "이름" --title "제목" --slides ./slides
```

Options:
- `--author`: 저자 이름 (필수)
- `--title`: PPT 제목 (필수)
- `--slides`: 슬라이드 HTML 폴더 (default: ./slides)
- `--output`: 출력 파일명 (default: YYMMDD_제목_저자.pptx)

html2pptx.js가 HTML → PowerPoint 변환 수행.
이미지 하이퍼링크 자동 처리됨.

## Key Points

- **링크 색상**: PPT 변환 시 CSS class 무시됨 → inline style 필수
- **이미지 링크**: `<a><img></a>` 구조로 하이퍼링크 자동 연결
- **원문 보존**: 사용자 원문 그대로 사용, 축약/의역 금지
- **계층 구조**: indent로 항목 관계 표현 (margin-left)
- **겸손한 톤**: "다 함" → "완료", 과장 금지

## Scripts

| Script | Purpose |
|--------|---------|
| `html2pptx.js` | HTML → PowerPoint 변환기 (이미지 하이퍼링크 지원) |
| `create_ppt.js` | PPT 생성 실행 스크립트 |
| `analyze_git_stats.py` | Git/GitLab/GitHub 통계 수집 |
| `analyze_strengths.py` | 장단점 분석 (LLM + 대화기록) |
| `fetch_youtube_thumbnail.py` | 유튜브 썸네일 다운로드 |

## References

| Reference | Purpose |
|-----------|---------|
| `company_template.md` | 회사 시무식 표준 목차 |
| `slide_template.md` | HTML 슬라이드 템플릿 + 색상 팔레트 |
| `input_format.md` | todo.txt 입력 형식 가이드 |
| `writing_guidelines.md` | PPT 작성 주의사항 (과장 금지, 원문 보존 등) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
