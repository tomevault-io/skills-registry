---
name: brainstorm-experiments-existing
description: 기존 제품의 가정을 검증하기 위한 실험을 설계합니다 — 프로토타입, A/B 테스트, 스파이크, 기타 저비용 검증 방법. 가정 검증, 기능 아이디어를 저렴하게 테스트, 또는 제품 실험 계획 시 사용합니다. Use when this capability is needed.
metadata:
  author: lucas-flatwhite
---

## 실험 설계 (기존 제품)

전체 구현에 착수하기 전에 제품 가정을 검증하기 위한 저비용 실험을 설계합니다.

### 맥락

**$ARGUMENTS**에 대한 실험을 설계하도록 제품 팀을 지원합니다. 팀에는 기능 아이디어와 검증이 필요한 가정이 있습니다.

사용자가 파일(PRD, 가정 목록, 디자인)을 제공하면 먼저 읽습니다.

### 지시 사항

사용자가 아이디어와 가정을 설명합니다. 다음 단계를 거칩니다:

1. **아이디어와 가정 명확화**: 팀이 무엇을 만들고 싶은지, 무엇을 검증해야 하는지 확인합니다.

2. **각 가정에 대한 실험 제안**. 다음과 같은 방법을 고려합니다:
   - 프로토타입으로 첫 번째 클릭 테스트 또는 작업 완료 테스트
   - 기능 스텁(stub) 또는 가짜 문 테스트(fake door)
   - 기술 스파이크(technical spike)
   - 프로덕션에서의 A/B 테스트 (위험 완화와 함께)
   - 오즈의 마법사 접근법 (Wizard of Oz)
   - 설문 기반 검증 (의견이 아닌 행동 기반)

3. **따라야 할 핵심 원칙**:
   - 사용자의 의견이 아닌 실제 행동을 측정합니다
   - 사용자나 비즈니스를 위험에 빠뜨리지 않도록 책임감 있게 테스트합니다
   - 프로덕션 테스트(예: A/B 테스트)의 경우 위험 완화 전략을 설명합니다
   - 최소한의 노력으로 최대한의 검증된 학습을 목표로 합니다

4. **각 실험에 대해** 다음을 명시합니다:
   - **가정**: 우리가 믿는 것은 무엇인가?
   - **실험**: 이를 검증하기 위해 정확히 무엇을 할 것인가?
   - **지표**: 무엇을 측정할 것인가?
   - **성공 임계값**: 우리가 옳다면 기대되는 값은?

단계별로 생각합니다. 실험을 명확한 표 또는 구조화된 형식으로 제시합니다. 내용이 많으면 마크다운으로 저장합니다.

---

### 추가 읽기

- [Testing Product Ideas: The Ultimate Validation Experiments Library](https://www.productcompass.pm/p/the-ultimate-experiments-library)
- [Assumption Prioritization Canvas: How to Identify And Test The Right Assumptions](https://www.productcompass.pm/p/assumption-prioritization-canvas)
- [What Is Product Discovery? The Ultimate Guide Step-by-Step](https://www.productcompass.pm/p/what-exactly-is-product-discovery)
- [Continuous Product Discovery Masterclass (CPDM)](https://www.productcompass.pm/p/cpdm) (영상 강좌)

---
> Source: [lucas-flatwhite/pm-skills-ko](https://github.com/lucas-flatwhite/pm-skills-ko) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
