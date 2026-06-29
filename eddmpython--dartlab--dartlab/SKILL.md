---
name: dartlab
description: KR 채권 시장 — 회사채 spread (AAA/AA/A/BBB) + 국고채 yield curve (1Y/3Y/5R/10Y) + butterfly. credit 엔진 (회사 단위) 와 macro 엔진 (시장 wide rates) 직교. fixedIncome 은 채권 시장 자체 분석. Use when this capability is needed.
metadata:
  author: eddmpython
---

## 엔진 역할

KR 채권 시장 — 회사채 spread (AAA/AA/A/BBB) + 국고채 yield curve (1Y/3Y/5R/10Y) + butterfly. credit 엔진 (회사 단위) 와 macro 엔진 (시장 wide rates) 직교. fixedIncome 은 채권 시장 자체 분석.

## 공개 호출 방식

```python
import dartlab
cs = dartlab.fixedIncome("krCorporateSpread", rating="AA", date="2026-05-28")
yc = dartlab.fixedIncome("kgbYieldCurve", date="2026-05-28")
```

## 호출 동작

KIS평가 (회사채 평균 spread) + KOFIA (국고채 yield) 일별. 252 일 z + regime 분류.

## 대표 반환 형태

axis 별 DataFrame 또는 dict. 공통 `tenor` (만기) + `rating` (신용등급) + `dateRef`.

## 기본 검증

- spread 단위 bp.
- rating enum 표준 (AAA/AA+/AA/AA-/A+/A/A-/BBB+/BBB/BBB-).
- tenor enum (3M/1Y/3Y/5Y/10Y/20Y/30Y).
- regime enum (compressed/normal/widening/extreme).

## 관련

- [engines.credit](/skills/engines.credit) — 회사 단위 신용 (회사채 spread 와 dCR rating 결합)
- [engines.macro](/skills/engines.macro) — 시장 wide rates axis

---
> Source: [eddmpython/dartlab](https://github.com/eddmpython/dartlab) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
