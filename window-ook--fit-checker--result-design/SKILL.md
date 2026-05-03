---
name: result-design
description: 지원자-회사 적합성 분석 결과를 시각적으로 미려한 HTML 프레젠테이션으로 제작하는 스킬. 매치율, 적합/부적합 요소, 보완 포인트를 lucide 아이콘과 Glassmorphism 스타일 표를 활용하여 직관적으로 표현. Use when this capability is needed.
metadata:
  author: window-ook
---

# 적합성 분석 결과 프레젠테이션 스킬

지원자와 회사 간의 적합성 분석 결과를 시각적으로 인상적인 프레젠테이션으로 제작합니다.

## 트리거 및 독립 실행

### 자동 활성화 조건

다음 **패턴 중 하나라도** 감지되면 이 스킬을 자동 활성화합니다:

**채용 공고 URL 감지:**

- `공고:`, `채용 공고:`, `채용:`, `job:`, `JD:` + URL
- 첫 번째 URL (순서상 첫 번째로 제공된 URL)

**회사 소개 URL/PDF 감지:**

- `회사 소개:`, `소개:`, `회사:`, `about:`, `기업:` + URL/PDF
- 두 번째 URL (순서상 두 번째로 제공된 URL)

**최소 조건:**

- URL 2개만 순서대로 제공되어도 활성화
  - 예: `https://recruit.example.com/123 https://example.com/about`
  - 첫 번째 URL → 채용 공고로 인식
  - 두 번째 URL → 회사 소개로 인식

**유연한 입력 예시:**

```
✅ 공고: https://... 회사 소개: https://...
✅ 채용 공고: https://... 소개: https://...
✅ https://recruit.com/job https://company.com/about
✅ JD: https://... about: https://...
✅ (URL1) (URL2)  ← 순서만 맞으면 OK
```

### 독립 실행 워크플로우

아래 순서로 분석을 수행합니다:

1. **URL 파싱** - 입력에서 채용 공고 URL과 회사 소개 URL 추출
2. **채용 공고 분석** - `job-posting-analyzer` 에이전트 호출
3. **회사 소개 분석** - `about-analyzer` 에이전트 호출
4. **지원자 정보 로드** - `.claude/skills/fit-check/references/applicant.md` 읽기
5. **선호 조건 로드** - `.claude/skills/fit-check/references/preference.md` 읽기
6. **적합성 분석** - 매치율 계산 로직 적용
7. **결과물 생성** - HTML 프레젠테이션 저장

## 디자인 철학

### 핵심 원칙

1. **명확한 정보 계층**: 가장 중요한 정보(매치율)가 가장 눈에 띄게
2. **데이터 시각화**: 숫자와 텍스트를 직관적인 그래픽으로 표현
3. **일관된 컬러 시스템**: 긍정/부정/중립 의미를 색상으로 전달
4. **lucide 아이콘 활용**: 정보의 빠른 인지를 돕는 시각적 앵커
5. **Light Mode Glassmorphism**: 깔끔하고 현대적인 반투명 카드 스타일

### 피해야 할 것

- 제네릭한 AI 생성 스타일
- 과도한 그라데이션이나 장식
- 읽기 어려운 폰트 조합
- 정보 과부하
- 이모지 사용 (lucide 아이콘으로 대체)

## 스타일 가이드

### 필수 CDN

```html
<link
  rel="stylesheet"
  as="style"
  crossorigin
  href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css"
/>
<script src="https://unpkg.com/lucide@latest"></script>
```

### 폰트

```css
font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, sans-serif;
```

- 제목: Pretendard Black (900), 32-48px
- 본문: Pretendard Regular, 14-16px
- 강조: Pretendard SemiBold (600)

### 컬러 팔레트

```css
:root {
  /* Theme Colors - Teal Based */
  --primary: #198175;
  --primary-light: oklch(72.3% 0.219 149.579);
  --primary-dark: #136b62;
  --primary-glow: rgba(45, 212, 140, 0.3);

  /* Status Colors - 5단계 적합도 */
  --grade-5: oklch(72.3% 0.219 149.579); /* 완벽 일치 */
  --grade-4: oklch(75% 0.2 149.579); /* 매우 적합 */
  --grade-3: #fbbf24; /* 적합 */
  --grade-2: #f97316; /* 부분 일치 */
  --grade-1: #ef4444; /* 미흡 */

  /* Semantic */
  --success: oklch(72.3% 0.219 149.579);
  --warning: #f59e0b;
  --danger: #ef4444;
  --neutral: #6b7280;

  /* Glassmorphism - Light Gradient Mode */
  --glass-bg: rgba(255, 255, 255, 0.7);
  --glass-bg-light: rgba(255, 255, 255, 0.6);
  --glass-border: rgba(255, 255, 255, 0.8);
  --glass-border-light: rgba(255, 255, 255, 0.9);
  --glass-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 10px 15px -3px rgba(0, 0, 0, 0.05),
    0 20px 25px -5px rgba(0, 0, 0, 0.03);
  --glass-blur: blur(20px);

  /* Text - Light Mode */
  --text-primary: #1f2937;
  --text-secondary: #374151;
  --text-muted: #6b7280;

  /* Background */
  --bg-gradient: linear-gradient(135deg, #f0fdfa 0%, #e0f2fe 50%, #f0fdf4 100%);
  --bg-card: rgba(255, 255, 255, 0.6);
}
```

### lucide 아이콘 가이드

| 용도                         | 아이콘         | 사용 예시                              |
| ---------------------------- | -------------- | -------------------------------------- |
| 섹션 2 타이틀 (잘 맞는 점)   | check-circle   | `<i data-lucide="check-circle"></i>`   |
| 섹션 3 타이틀 (맞지 않는 점) | alert-triangle | `<i data-lucide="alert-triangle"></i>` |
| 섹션 4 타이틀 (보완 포인트)  | lightbulb      | `<i data-lucide="lightbulb"></i>`      |
| 섹션 5 타이틀 (종합 요약)    | clipboard-list | `<i data-lucide="clipboard-list"></i>` |
| 매치율 근거                  | bar-chart-2    | `<i data-lucide="bar-chart-2"></i>`    |
| 강점 카드                    | thumbs-up      | `<i data-lucide="thumbs-up"></i>`      |
| 주의 카드                    | alert-circle   | `<i data-lucide="alert-circle"></i>`   |
| 액션 카드                    | zap            | `<i data-lucide="zap"></i>`            |
| 리스트 화살표                | chevron-right  | `<i data-lucide="chevron-right"></i>`  |
| 근거                         | bookmark       | `<i data-lucide="bookmark"></i>`       |
| 제안                         | lightbulb      | `<i data-lucide="lightbulb"></i>`      |
| 필수 요건 불충족             | x-circle       | `<i data-lucide="x-circle"></i>`       |
| 선호 조건                    | star           | `<i data-lucide="star"></i>`           |
| 우대 사항                    | briefcase      | `<i data-lucide="briefcase"></i>`      |
| 확인 필요                    | help-circle    | `<i data-lucide="help-circle"></i>`    |
| 적합도 체크                  | check          | `<i data-lucide="check"></i>`          |
| 중요도                       | flame          | `<i data-lucide="flame"></i>`          |
| 충족도                       | pie-chart      | `<i data-lucide="pie-chart"></i>`      |
| 정보                         | info           | `<i data-lucide="info"></i>`           |

### 카테고리 아이콘

| 카테고리 | 아이콘     | 클래스                 | 컬러                       |
| -------- | ---------- | ---------------------- | -------------------------- |
| 기술     | code-2     | .category-icon.tech    | #60a5fa                    |
| AI       | bot        | .category-icon.ai      | #a78bfa                    |
| UI       | palette    | .category-icon.ui      | #f472b6                    |
| 도메인   | building-2 | .category-icon.domain  | oklch(72.3% 0.219 149.579) |
| 출퇴근   | train      | .category-icon.commute | #fbbf24                    |

### 5단계 적합도 배지 시스템

| 등급 | 라벨      | CSS 클래스           | 사용 시점            |
| ---- | --------- | -------------------- | -------------------- |
| 5    | 완벽 일치 | .grade-badge.grade-5 | 요구사항 100% 충족   |
| 4    | 매우 적합 | .grade-badge.grade-4 | 요구사항 대부분 충족 |
| 3    | 적합      | .grade-badge.grade-3 | 기본 충족            |
| 2    | 부분 일치 | .grade-badge.grade-2 | 일부만 충족          |
| 1    | 미흡      | .grade-badge.grade-1 | 요구사항 미충족      |

## 프레젠테이션 구조

### 슬라이드 1: 타이틀 & 매치율

```html
<div class="glass-card header">
  <div class="company-badge">
    <i data-lucide="building-2" style="width: 16px; height: 16px;"></i>
    적합성 분석 결과
  </div>
  <h1>[회사명]</h1>
  <p class="position">[직무] | [도메인]</p>

  <div class="score-section">
    <div class="donut-chart">
      <svg width="200" height="200" viewBox="0 0 200 200">
        <defs>
          <linearGradient id="tealGradient" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" style="stop-color:#14b8a6" />
            <stop offset="100%" style="stop-color:#0d9488" />
          </linearGradient>
        </defs>
        <circle class="donut-bg" cx="100" cy="100" r="80" />
        <circle
          class="donut-progress"
          cx="100"
          cy="100"
          r="80"
          stroke-dasharray="[계산값] 503"
        />
        <!-- stroke-dasharray 계산: [매치율] * 5.03 -->
      </svg>
      <div class="donut-center">
        <div class="score">[매치율]%</div>
      </div>
    </div>

    <div class="score-reasons">
      <div class="reason-item">
        <div class="reason-icon green">
          <i data-lucide="[아이콘]" style="width: 20px; height: 20px;"></i>
        </div>
        <div class="reason-text">
          <strong>[첫 번째 근거 제목]</strong><br />
          [첫 번째 근거 설명]
        </div>
      </div>
      <div class="reason-item">
        <div class="reason-icon blue">
          <i data-lucide="[아이콘]" style="width: 20px; height: 20px;"></i>
        </div>
        <div class="reason-text">
          <strong>[두 번째 근거 제목]</strong><br />
          [두 번째 근거 설명]
        </div>
      </div>
      <div class="reason-item">
        <div class="reason-icon yellow">
          <i data-lucide="[아이콘]" style="width: 20px; height: 20px;"></i>
        </div>
        <div class="reason-text">
          <strong>[세 번째 근거 제목]</strong><br />
          [세 번째 근거 설명]
        </div>
      </div>
    </div>
  </div>
</div>
```

### 슬라이드 2: 잘 맞는 점 (Strengths)

```html
<div class="section">
  <div class="section-header">
    <div class="section-icon green">
      <i data-lucide="check-circle" style="width: 24px; height: 24px;"></i>
    </div>
    <h2 class="section-title">잘 맞는 점</h2>
  </div>

  <div class="glass-card" style="overflow: hidden;">
    <table class="strengths-table">
      <thead>
        <tr>
          <th style="width: 25%;">요구사항</th>
          <th style="width: 55%;">지원자 역량</th>
          <th style="width: 20%;">적합도</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td class="requirement-cell">[요구사항]</td>
          <td class="match-cell">
            <strong>[핵심 역량]</strong><br />
            [상세 설명]
          </td>
          <td class="badge-cell">
            <span class="fit-badge level-5">
              <i data-lucide="star" style="width: 14px; height: 14px;"></i>
              완벽 일치
            </span>
          </td>
        </tr>
        <!-- 추가 행들 -->
      </tbody>
    </table>
  </div>
</div>
```

### 슬라이드 3: 맞지 않는 점 (Gaps)

```html
<div class="section">
  <div class="section-header">
    <div class="section-icon red">
      <i data-lucide="alert-triangle" style="width: 24px; height: 24px;"></i>
    </div>
    <h2 class="section-title">맞지 않는 점</h2>
  </div>

  <div class="gap-grid">
    <!-- 선호 조건 -->
    <div class="gap-card prefer">
      <div class="gap-card-header">
        <span class="gap-card-title">[항목명]</span>
        <span class="gap-type-badge prefer">선호조건</span>
      </div>
      <div class="gap-card-content">[설명 내용]</div>
    </div>

    <!-- 확인 필요 -->
    <div class="gap-card info">
      <div class="gap-card-header">
        <span class="gap-card-title">[항목명]</span>
        <span class="gap-type-badge info">확인필요</span>
      </div>
      <div class="gap-card-content">[설명 내용]</div>
    </div>

    <!-- 필수 요건 (critical) -->
    <div class="gap-card critical">
      <div class="gap-card-header">
        <span class="gap-card-title">[항목명]</span>
        <span class="gap-type-badge critical">필수요건</span>
      </div>
      <div class="gap-card-content">[설명 내용]</div>
    </div>
  </div>
</div>
```

### 슬라이드 4: 보완해야 할 요인

```html
<div class="section">
  <div class="section-header">
    <div class="section-icon yellow">
      <i data-lucide="lightbulb" style="width: 24px; height: 24px;"></i>
    </div>
    <h2 class="section-title">보완 포인트</h2>
  </div>
  <section class="slide improvements-slide">
    <div class="improvement-list">
      <div class="improvement-item">
        <div class="improvement-badges">
          <span class="importance-badge high">
            <i data-lucide="flame"></i>
            <span class="label">중요도</span>
            높음
          </span>
          <span class="fulfillment-badge low">
            <i data-lucide="pie-chart"></i>
            <span class="label">충족도</span>
            0%
          </span>
        </div>
        <h3>[보완 항목명]</h3>
        <p class="description">[보완이 필요한 이유 설명]</p>
        <div class="evidence">
          <strong><i data-lucide="bookmark"></i> 근거</strong>
          <p>[채용 공고나 회사 정보에서 발췌한 근거]</p>
        </div>
        <div class="suggestion">
          <strong><i data-lucide="lightbulb"></i> 제안</strong>
          <p>[개선 방법 또는 어필 포인트]</p>
        </div>
      </div>

      <div class="improvement-item">
        <div class="improvement-badges">
          <span class="importance-badge medium">
            <i data-lucide="flame"></i>
            <span class="label">중요도</span>
            중간
          </span>
          <span class="fulfillment-badge medium">
            <i data-lucide="pie-chart"></i>
            <span class="label">충족도</span>
            50%
          </span>
        </div>
        <h3>[보완 항목명]</h3>
        <p class="description">[설명]</p>
        <div class="evidence">
          <strong><i data-lucide="bookmark"></i> 근거</strong>
          <p>[근거]</p>
        </div>
        <div class="suggestion">
          <strong><i data-lucide="lightbulb"></i> 제안</strong>
          <p>[제안]</p>
        </div>
      </div>

      <div class="improvement-item">
        <div class="improvement-badges">
          <span class="importance-badge low">
            <i data-lucide="flame"></i>
            <span class="label">중요도</span>
            낮음
          </span>
          <span class="fulfillment-badge medium">
            <i data-lucide="pie-chart"></i>
            <span class="label">충족도</span>
            확인 필요
          </span>
        </div>
        <h3>[보완 항목명]</h3>
        <p class="description">[설명]</p>
        <div class="evidence">
          <strong><i data-lucide="bookmark"></i> 근거</strong>
          <p>[근거]</p>
        </div>
        <div class="suggestion">
          <strong><i data-lucide="lightbulb"></i> 제안</strong>
          <p>[제안]</p>
        </div>
      </div>
    </div>
  </section>
</div>
```

### 슬라이드 5: 요약 및 결론

```html
<div class="section">
  <div class="section-header">
    <div class="section-icon teal">
      <i data-lucide="file-text" style="width: 24px; height: 24px;"></i>
    </div>
    <h2 class="section-title">종합 요약</h2>
  </div>

  <div class="glass-card" style="padding: 24px;">
    <div class="summary-grid">
      <div class="summary-card">
        <div class="summary-card-header">
          <div class="summary-card-icon green">
            <i data-lucide="trophy" style="width: 20px; height: 20px;"></i>
          </div>
          <span class="summary-card-title">핵심 강점</span>
        </div>
        <div class="summary-card-content">
          <div class="summary-item">
            <i data-lucide="check" style="width: 16px; height: 16px;"></i>
            [강점 1]
          </div>
          <div class="summary-item">
            <i data-lucide="check" style="width: 16px; height: 16px;"></i>
            [강점 2]
          </div>
        </div>
      </div>

      <div class="summary-card">
        <div class="summary-card-header">
          <div class="summary-card-icon yellow">
            <i
              data-lucide="alert-circle"
              style="width: 20px; height: 20px;"
            ></i>
          </div>
          <span class="summary-card-title">주의 사항</span>
        </div>
        <div class="summary-card-content">
          <div class="summary-item">
            <i data-lucide="minus" style="width: 16px; height: 16px;"></i>
            [주의 1]
          </div>
          <div class="summary-item">
            <i data-lucide="minus" style="width: 16px; height: 16px;"></i>
            [주의 2]
          </div>
        </div>
      </div>

      <div class="summary-card">
        <div class="summary-card-header">
          <div class="summary-card-icon blue">
            <i data-lucide="rocket" style="width: 20px; height: 20px;"></i>
          </div>
          <span class="summary-card-title">추천 액션</span>
        </div>
        <div class="summary-card-content">
          <div class="summary-item">
            <i data-lucide="arrow-right" style="width: 16px; height: 16px;"></i>
            [액션 1]
          </div>
          <div class="summary-item">
            <i data-lucide="arrow-right" style="width: 16px; height: 16px;"></i>
            [액션 2]
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

### Footer

```html
<div class="footer">
  <a
    href="https://github.com/window-ook/fit-checker"
    target="_blank"
    rel="noopener noreferrer"
    class="footer-badge"
    onclick="handleBadgeClick(event)"
  >
    <i data-lucide="github"></i>
    <span class="brand-name">Fit Checker</span>
    <span class="divider"></span>
    <span class="author">window-ook</span>
    <i data-lucide="external-link"></i>
  </a>
</div>
```

## 매치율 계산 로직

```
매치율 = (충족 항목 가중치 합 / 전체 항목 가중치 합) × 100

가중치 배분:
┌─────────────────┬────────┬─────────────────────────────┐
│ 항목            │ 가중치 │ 설명                        │
├─────────────────┼────────┼─────────────────────────────┤
│ 필수 자격요건   │ 30점   │ 경력, 학력, 필수 기술       │
│ 기술 스택 일치  │ 25점   │ 기술 스택 매칭 비율         │
│ 우대 사항 충족  │ 20점   │ 우대 조건 충족 비율         │
│ 문화 적합성     │ 15점   │ 팀 분위기, 업무 방식 매칭   │
│ 기타 조건       │ 10점   │ 위치, 연봉 등 (선호 조건)   │
└─────────────────┴────────┴─────────────────────────────┘

※ 선호 조건(preference.md)은 참고용이며, 불충족 시 큰 감점 없음
```

## 선호 vs 필수 구분

적합성 분석 시 다음을 명확히 구분합니다:

| 구분              | 출처               | 가중치 | 불충족 시         |
| ----------------- | ------------------ | ------ | ----------------- |
| **필수 (Must)**   | 채용 공고 자격요건 | 높음   | 매치율 크게 감소  |
| **선호 (Prefer)** | preference.md      | 낮음   | 참고용, 작은 감점 |
| **우대 (Plus)**   | 채용 공고 우대사항 | 중간   | 없어도 무방       |

## 출력 파일

결과물은 `output/` 디렉토리에 저장됩니다:

- `output/fit-result-[회사명]-YYYY-MM-DD.html` - 메인 프레젠테이션
- `output/fit-result-[회사명]-YYYY-MM-DD.md` - 마크다운 버전 (사용자 요청 시)
- `output/fit-result-[회사명]-YYYY-MM-DD.pptx` - PPTX 버전 (make-pptx 스킬 사용)

## CSS 템플릿

결과물에 **반드시** 포함해야 할 전체 스타일:

```css
:root {
  /* Theme Colors - Teal Based */
  --primary: #198175;
  --primary-light: oklch(72.3% 0.219 149.579);
  --primary-dark: #136b62;
  --primary-glow: rgba(45, 212, 140, 0.3);

  /* Status Colors */
  --grade-5: oklch(72.3% 0.219 149.579);
  --grade-4: oklch(75% 0.2 149.579);
  --grade-3: #fbbf24;
  --grade-2: #f97316;
  --grade-1: #ef4444;

  --success: oklch(72.3% 0.219 149.579);
  --warning: #f59e0b;
  --danger: #ef4444;
  --neutral: #6b7280;

  /* Glassmorphism - Light Mode */
  --glass-bg: rgba(0, 0, 0, 0.02);
  --glass-bg-light: rgba(0, 0, 0, 0.04);
  --glass-border: rgba(0, 0, 0, 0.08);
  --glass-border-light: rgba(0, 0, 0, 0.12);
  --glass-shadow: 0 8px 32px rgba(0, 0, 0, 0.08);
  --glass-blur: blur(12px);

  /* Text - Light Mode */
  --text-primary: #000000;
  --text-secondary: #374151;
  --text-muted: #4b5563;

  /* Backgrounds - Light Mode */
  --bg-dark: #ffffff;
  --bg-card: rgba(0, 0, 0, 0.02);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, sans-serif;
  background: var(--bg-gradient);
  min-height: 100vh;
  padding: 40px 20px;
  color: var(--text-primary);
  line-height: 1.6;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
}

/* Glass Card Base */
.glass-card {
  background: #ffffff;
  backdrop-filter: var(--glass-blur);
  -webkit-backdrop-filter: var(--glass-blur);
  border: 1px solid var(--glass-border);
  border-radius: 20px;
  box-shadow: var(--glass-shadow);
}

.slide {
  margin-bottom: 32px;
  overflow: hidden;
}

/* ===== SECTION 1: Header & Match Rate ===== */
.header {
  padding: 48px;
  margin-bottom: 32px;
  text-align: center;
}

.company-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  background: linear-gradient(135deg, var(--primary), var(--primary-dark));
  color: white;
  padding: 8px 20px;
  border-radius: 100px;
  font-size: 14px;
  font-weight: 600;
  margin-bottom: 20px;
}

.header h1 {
  font-size: 36px;
  font-weight: 800;
  color: var(--text-primary);
  margin-bottom: 8px;
}

.header .position {
  font-size: 18px;
  color: var(--text-muted);
  margin-bottom: 32px;
}

/* Score Section */
.score-section {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 60px;
  flex-wrap: wrap;
}

.donut-chart {
  position: relative;
  width: 200px;
  height: 200px;
}

.donut-chart svg {
  transform: rotate(-90deg);
}

.donut-chart circle {
  fill: none;
  stroke-width: 20;
}

.donut-bg {
  stroke: #e5e7eb;
}

.donut-progress {
  stroke: url(#tealGradient);
  stroke-linecap: round;
  transition: stroke-dasharray 1s ease;
}

.donut-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
}

.donut-center .score {
  font-size: 48px;
  font-weight: 800;
  background: linear-gradient(135deg, var(--primary), var(--primary-dark));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.score-reasons {
  display: flex;
  flex-direction: column;
  gap: 16px;
  max-width: 400px;
}

.reason-item {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 16px 20px;
  background: var(--glass-bg-light);
  border-radius: 16px;
  border: 1px solid var(--glass-border);
}

.reason-icon {
  width: 36px;
  height: 36px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.reason-icon.green {
  background: #d1fae5;
  color: #16a34a;
}

.reason-icon.yellow {
  background: #fef3c7;
  color: #d97706;
}

.reason-icon.blue {
  background: #dbeafe;
  color: #2563eb;
}

.reason-text {
  font-size: 14px;
  line-height: 1.5;
  color: var(--text-secondary);
}

.reason-text strong {
  color: var(--text-primary);
}

/* ===== SECTION 2: Strengths Table ===== */
.section {
  margin-bottom: 32px;
}

.section-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 24px;
  padding: 0 8px;
}

.section-icon {
  width: 44px;
  height: 44px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.section-icon.green {
  background: linear-gradient(135deg, #22c55e, #16a34a);
  color: white;
}

.section-icon.red {
  background: linear-gradient(135deg, #ef4444, #dc2626);
  color: white;
}

.section-icon.blue {
  background: linear-gradient(135deg, #3b82f6, #2563eb);
  color: white;
}

.section-icon.yellow {
  background: linear-gradient(135deg, #f59e0b, #d97706);
  color: white;
}

.section-icon.teal {
  background: linear-gradient(135deg, var(--primary), var(--primary-dark));
  color: white;
}

.section-title {
  font-size: 24px;
  font-weight: 700;
  color: var(--text-primary);
}

/* Strengths Table */
.strengths-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
}

.strengths-table thead th {
  background: var(--glass-bg);
  padding: 16px 20px;
  text-align: left;
  font-weight: 600;
  font-size: 13px;
  color: var(--text-muted);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.strengths-table thead th:first-child {
  border-radius: 16px 0 0 0;
}

.strengths-table thead th:last-child {
  border-radius: 0 16px 0 0;
  text-align: center;
}

.strengths-table tbody td {
  padding: 20px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.05);
  vertical-align: top;
}

.strengths-table tbody tr:last-child td {
  border-bottom: none;
}

.strengths-table tbody tr:last-child td:first-child {
  border-radius: 0 0 0 16px;
}

.strengths-table tbody tr:last-child td:last-child {
  border-radius: 0 0 16px 0;
}

.requirement-cell {
  font-weight: 600;
  color: var(--text-primary);
}

.match-cell {
  color: var(--text-secondary);
  line-height: 1.6;
}

.match-cell strong {
  color: var(--primary);
}

.badge-cell {
  text-align: center;
}

/* Fit Badge (5-level) */
.fit-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 6px 14px;
  border-radius: 100px;
  font-size: 13px;
  font-weight: 600;
}

.fit-badge.level-5 {
  background: linear-gradient(135deg, #ccfbf1, #f0fdfa);
  color: #0f766e;
  border: 1px solid #99f6e4;
}

.fit-badge.level-4 {
  background: linear-gradient(135deg, #d1fae5, #ecfdf5);
  color: #16a34a;
  border: 1px solid #a7f3d0;
}

.fit-badge.level-3 {
  background: linear-gradient(135deg, #dbeafe, #eff6ff);
  color: #2563eb;
  border: 1px solid #bfdbfe;
}

.fit-badge.level-2 {
  background: linear-gradient(135deg, #fef3c7, #fffbeb);
  color: #d97706;
  border: 1px solid #fde68a;
}

.fit-badge.level-1 {
  background: linear-gradient(135deg, #fee2e2, #fef2f2);
  color: #dc2626;
  border: 1px solid #fecaca;
}

/* ===== SECTION 3: Gaps ===== */
.gap-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(340px, 1fr));
  gap: 20px;
}

.gap-card {
  padding: 24px;
  border-radius: 20px;
  background: #fff;
  border: 1px solid var(--glass-border);
}

.gap-card.critical {
  border-left: 4px solid #ef4444;
}

.gap-card.prefer {
  border-left: 4px solid #f59e0b;
}

.gap-card.info {
  border-left: 4px solid #9ca3af;
}

.gap-card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 12px;
}

.gap-card-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-primary);
}

.gap-type-badge {
  font-size: 11px;
  font-weight: 600;
  padding: 4px 10px;
  border-radius: 100px;
  text-transform: uppercase;
}

.gap-type-badge.critical {
  background: #fee2e2;
  color: #dc2626;
}

.gap-type-badge.prefer {
  background: #fef3c7;
  color: #d97706;
}

.gap-type-badge.info {
  background: #f3f4f6;
  color: #4b5563;
}

.gap-card-content {
  font-size: 14px;
  line-height: 1.6;
  color: var(--text-secondary);
}

/* ===== SECTION 4: Improvements ===== */
.improvements-slide {
  padding: 48px;
  background: linear-gradient(
    135deg,
    var(--primary) 0%,
    var(--primary-dark) 100%
  );
  backdrop-filter: var(--glass-blur);
  -webkit-backdrop-filter: var(--glass-blur);
  border: 1px solid var(--glass-border);
  border-radius: 20px;
}

.improvement-list {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.improvement-item {
  padding: 28px;
  background: #fff;
  border: 1px solid var(--glass-border);
  border-radius: 16px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -2px rgba(0, 0, 0, 0.1);
}

.improvement-badges {
  display: flex;
  gap: 12px;
  margin-bottom: 20px;
}

.importance-badge,
.fulfillment-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 8px 14px;
  border-radius: 10px;
  font-size: 13px;
  font-weight: 600;
  background: var(--glass-bg);
  border: 1px solid var(--glass-border);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.importance-badge i,
.fulfillment-badge i {
  width: 16px;
  height: 16px;
}

.importance-badge .label,
.fulfillment-badge .label {
  font-size: 11px;
  color: var(--text-muted);
  margin-right: 4px;
}

.importance-badge.high {
  color: var(--danger);
  border-color: rgba(239, 68, 68, 0.3);
}
.importance-badge.medium {
  color: var(--warning);
  border-color: rgba(245, 158, 11, 0.3);
}
.importance-badge.low {
  color: var(--primary-light);
  border-color: rgba(45, 212, 140, 0.3);
}

.fulfillment-badge.low {
  color: var(--danger);
  border-color: rgba(239, 68, 68, 0.3);
}
.fulfillment-badge.medium {
  color: var(--warning);
  border-color: rgba(245, 158, 11, 0.3);
}
.fulfillment-badge.high {
  color: var(--primary-light);
  border-color: rgba(45, 212, 140, 0.3);
}

.improvement-item h3 {
  font-size: 20px;
  font-weight: 700;
  margin-bottom: 12px;
  color: var(--text-primary);
}

.improvement-item .description {
  color: var(--text-secondary);
  margin-bottom: 20px;
  font-size: 15px;
  line-height: 1.7;
}

.evidence,
.suggestion {
  padding: 18px;
  border-radius: 12px;
  margin-bottom: 12px;
  font-size: 14px;
  background: var(--glass-bg);
  border: 1px solid var(--glass-border);
}

.evidence {
  border-left: 3px solid var(--warning);
}

.suggestion {
  border-left: 3px solid var(--primary);
}

.evidence strong,
.suggestion strong {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 10px;
  font-size: 13px;
  color: var(--text-secondary);
}

.evidence strong i,
.suggestion strong i {
  width: 16px;
  height: 16px;
}

.evidence strong {
  color: var(--warning);
}
.suggestion strong {
  color: var(--primary-light);
}

.evidence p,
.suggestion p {
  color: var(--text-secondary);
  line-height: 1.7;
}

/* ===== SECTION 5: Summary ===== */
.summary-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}

@media (max-width: 900px) {
  .summary-grid {
    grid-template-columns: 1fr;
  }
}

.summary-card {
  padding: 28px;
  border-radius: 20px;
  background: var(--glass-bg-light);
  border: 1px solid var(--glass-border);
}

.summary-card-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
}

.summary-card-icon {
  width: 40px;
  height: 40px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.summary-card-icon.green {
  background: #d1fae5;
  color: #16a34a;
}

.summary-card-icon.yellow {
  background: #fef3c7;
  color: #d97706;
}

.summary-card-icon.blue {
  background: #dbeafe;
  color: #2563eb;
}

.summary-card-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-primary);
}

.summary-card-content {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.summary-item {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  font-size: 14px;
  line-height: 1.5;
  color: var(--text-secondary);
}

.summary-item i {
  flex-shrink: 0;
  margin-top: 2px;
  color: #9ca3af;
}

/* Footer */
.footer {
  text-align: center;
  padding: 32px;
}

.footer-badge {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  padding: 12px 24px;
  background: linear-gradient(
    135deg,
    var(--primary) 0%,
    var(--primary-dark) 100%
  );
  border: none;
  border-radius: 50px;
  color: #ffffff;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  text-decoration: none;
  box-shadow: 0 4px 15px rgba(25, 129, 117, 0.3);
  transition: all 0.3s ease;
}

.footer-badge:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(25, 129, 117, 0.4);
  background: linear-gradient(
    135deg,
    var(--primary-light) 0%,
    var(--primary) 100%
  );
}

.footer-badge:active {
  transform: translateY(0);
}

.footer-badge i {
  width: 18px;
  height: 18px;
}

.footer-badge .brand-name {
  font-weight: 700;
  color: #ffffff;
}

.footer-badge .divider {
  width: 1px;
  height: 16px;
  background: rgba(255, 255, 255, 0.3);
}

.footer-badge .author {
  opacity: 0.9;
}

/* Responsive */
@media (max-width: 768px) {
  .title-slide {
    padding: 40px 24px;
  }

  .title-slide .company-name {
    font-size: 32px;
  }

  .match-circle {
    width: 180px;
    height: 180px;
  }

  .match-circle::before {
    width: 145px;
    height: 145px;
  }

  .rate-value {
    font-size: 48px;
  }

  .comparison {
    flex-direction: column;
    gap: 12px;
  }

  .improvement-badges {
    flex-wrap: wrap;
  }
}
```

## JavaScript 초기화

HTML 파일 하단에 반드시 포함:

```html
<script>
  lucide.createIcons();

  function handleBadgeClick(event) {
    const badge = event.currentTarget;
    badge.style.transform = 'scale(0.95)';
    setTimeout(() => {
      badge.style.transform = '';
    }, 100);
  }
</script>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/window-ook) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
