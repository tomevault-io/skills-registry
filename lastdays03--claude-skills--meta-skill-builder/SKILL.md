---
name: meta-skill-builder
description: 표준 참조 분리 패턴(Standard Reference Separation Pattern)을 사용하여 워크플로우를 새로 생성하거나 개선합니다. Use when this capability is needed.
metadata:
  author: lastdays03
---

# 메타 스킬 빌더 (Meta Skill Builder)

이 워크플로우는 Antigravity의 "Reference Separation Pattern"에 따라 에이전트 스킬을 **생성(✨)**하거나 **개선(🔧)**하도록 돕습니다.

## 1. 초기화 (Initialization)

1.  **메타 스킬 로드**: 엔지니어링 표준을 읽습니다.
    - `SKILL.md` (Self)를 읽어 각 모드의 목표와 완료 조건(DoD)을 파악합니다. (주의: 영어로 작성됨)
    - `resources/checklist.md`를 읽어 검증 준비를 합니다. (주의: 영어로 작성됨)

2.  **태스크 초기화**: `task.md`를 시작합니다.
    - TaskName: "Meta Skill Engineering"
    - Status: "사용자 의도 파악 중 (Creation vs Refinement)"

## 2. 모드 식별 및 계획 (Identify Mode & Plan)

1.  **모드 식별**: 사용자 요청을 분석하여 모드를 결정하고 사용자에게 알립니다.
    - **✨ Creation Mode**: 새로운 아이디어, "만들어줘", "외부 스킬 가져와줘".
    - **🔧 Refinement Mode**: 기존 파일, "리팩토링해줘", "이 레퍼런스 참고해서 개선해줘".

2.  **사전 탐색 (Strict Discovery Protocol)**: **[검증 필수]**
    - `resources/discovery-guide.md`의 **Tier 1 (Official)** 소스부터 확인합니다.
    - **Step 1: Source Selection**:
        - GitHub `anthropics/skills` 또는 `obra/superpowers` 등 검증된 리포지토리 우선 검색.
        - 단순 웹 검색보다 **신뢰할 수 있는 소스**를 찾았는지 확인합니다.
    - **Step 2: Quality Check**:
        - 찾은 소스가 **CoT(Chain of Thought)**와 **Few-shot 예제**를 포함하는지 확인합니다.
    - 검색 결과가 없다면: "Tier 1 소스 없음 확인. 로컬 표준 템플릿을 사용합니다."라고 명시.

3.  **이름 결정 (생성 모드만)**: 생성할 경우 스킬 이름을 정합니다.
    - `SKILL.md`의 명명 규칙(`dev-`, `obsi-` 등)을 참고합니다.
    - 확신이 서지 않으면 사용자에게 확인합니다.

4.  **계획 수립**: `implementation_plan.md`를 작성합니다.
    - 표준 구조를 사용합니다.
    - **언어 규칙 (Language Rule)**:
        - Trigger (`workflows/*.md`): **한국어 (Korean)** - 사용자 가독성 위주.
        - Skill (`skills/*/SKILL.md`): **영어 (English)** - 에이전트 효율성 위주.
        - Templates: **3-Tier Strategy** (SKILL.md 참조) - 파일 성격에 따라 영어/하이브리드/한글 적용.

## 3. 실행 (Execution - Select Path)

### 경로 A: ✨ Creation Mode
1.  **디렉토리 생성**: `.agent/skills/{skill_name}/` 폴더를 만듭니다.
2.  **자산 생성 (Asset Creation)**:
    - **만약 외부 소스가 있다면**: `resources/snippet-extractor-guide.md`의 규칙에 따라 핵심 로직을 추출하여 `SKILL.md`를 구성합니다.
    - **없다면(로컬)**: 스킬 성격에 맞춰 `resources/coding-template.md`, `resources/doc-template.md` 등 하나를 선택해 복사합니다.
    - **공통**: `workflows/{skill_name}.md` (Trigger)를 생성하여 "Invoke the {skill_name} skill..." 내용을 포함합니다.
3.  **검증 (Verification)**:
    - `resources/discovery-guide.md`의 "2. 평가 기준"을 충족하는지 확인합니다.
    - **자동 검증 실행**:
        ```bash
        python3 scripts/validate_skill.py {skill_name}
        ```

### 경로 B: 🔧 Refinement Mode
1.  **진단 (Diagnosis)**:
    - **Gap Analysis**: 만약 외부 소스가 있다면, 현재 파일과 비교하여 부족한 점(Gap)을 찾습니다.
    - **Internal Check**: `resources/checklist.md`와 `SKILL.md`의 **"Reference Separation"** 및 **"3-Tier Strategy"** 기준을 확인합니다.
2.  **리팩토링 (Refactoring)**:
    - 발견된 Gap과 구조적 문제를 해결합니다.
    - 로직 분리 (Trigger <-> Skill).
    - 템플릿과 표준을 `.agent/skills/{skill_name}/SKILL.md` (영어)로 이동합니다.
    - `workflows/{skill_name}.md` (한국어)는 트리거 역할만 하도록 단순화합니다.

## 4. 검증 (Verification)

1.  **자동 검증 (Auto-Validation)**:
    - 생성된 스킬이 프로젝트 표준을 준수하는지 스크립트로 확인합니다.
    ```bash
    python3 scripts/validate_skill.py {skill_name}
    ```

2.  **완료**:
    - `task.md`를 'Done'으로 업데이트합니다.
    - 생성/개선된 파일 목록을 요약하여 사용자에게 알립니다.


---

## Standards & Rules

# Meta-Skill: Workflow Engineering

This document defines the core principles and standards for the **"Skill Builder"** workflow, specifically for Creating and Refining agent skills in Antigravity.

---

## 💎 1. Core Principles

1.  **Trigger & Skill Pattern** (formerly Reference Separation):
    - **Trigger** (`workflows/*.md`): Minimal entry point.
    - **Skill** (`skills/*/SKILL.md`): Unified logic and standards.
    - Data/Templates MUST go to `skills/*/resources/`.
2.  **User-Centric Interaction**:
    - Explicitly request inputs and use confirmation gates (`notify_user`).
3.  **Language Separation Strategy 🇰🇷/🇺🇸**:
    - Trigger (`workflows/*.md`): **Korean** (User Readability)
    - Skill (`skills/*/SKILL.md`): **Hybrid**
        - **Workflow Steps**: **Korean 🇰🇷** (Intuitive procedure)
        - **Standards & Rules**: **English 🇺🇸** (Strict constraint)
    - Templates (`skills/*/resources/*-template.md`): **3-Tier Strategy**
        1.  **Agent-Facing** (e.g. `coding-template`, `doc-template`): **English 🇺🇸**
        2.  **User-Heavy** (e.g. `plan-template`): **Hybrid (KR Headers + EN Logic) 🇰🇷/🇺🇸**
        3.  **User-Light** (e.g. `overview-template`): **Korean 🇰🇷**

---

## 🏗️ 2. Operating Modes (Process)

You MUST identify the mode and then decide the **Reference Source**.

### ✨ Creation Mode
- **Trigger**: "Create a new workflow", "Import this GitHub skill", "I need a skill to automate X"
- **Goal**: Convert an idea OR an external resource into a concrete **"File Set"**.
- **Process**:
    1.  **Search**: Always look for external references (GitHub/Web) first.
    2.  **Adapt**: If found, use `resources/snippet-extractor-guide.md`. If not, use local templates.
- **Definition of Done (DoD)**:
    1.  Create `.agent/workflows/{name}.md` and `.agent/skills/{name}/`.
    2.  Create `SKILL.md` inside the skill folder and link it properly.
    3.  Organize assets into `scripts/` and `resources/`.

### 🔧 Refinement Mode
- **Trigger**: "Refine this workflow", "Update this skill with new standard"
- **Goal**: Resolve structural debt OR upgrade using a better external reference.
- **Process**:
    1.  **Search**: Look for "better ways to do this" externally.
    2.  **Compare**: Check gap between current file and external "Gold Standard".
    3.  **Refactor**: Apply improvements (Trigger/Skill Separation, etc.).
- **Definition of Done (DoD)**:
    1.  Are templates separated into `resources/`?
    2.  Does `SKILL.md` define the Gold Standard?
    3.  Are `task.md` and `notify_user` used appropriately?

---

## 🏆 3. Quality Standards

All workflows must meet the following criteria:

### 1) Trigger & Skill Structure
- **Trigger**: `workflows/*.md` must act ONLY as an invocation pointer.
- **Skill**: All logic, rules, and procedures must reside in `skills/{name}/SKILL.md`.
- **Assets**: Scripts and templates must be in `skills/{name}/scripts` and `skills/{name}/resources`.

### 2) Safe YAML Frontmatter 🛡️
- **Quoting**: The `description` field MUST be wrapped in double quotes `""` if it contains Korean or special characters `(:)`.
    - Bad: `description: 체계적인 디버깅(Systematic)`
    - Good: `description: "체계적인 디버깅(Systematic)"`

### 3) User-Centric Interaction
- **Explicit Inputs**: Clearly request what input is needed from the user.
- **Confirmation Gates**: Use `notify_user` to verify before creating or modifying critical files.

### 4) Self-Contained Context
- The workflow file alone should explain "What this skill does" (Description Header).
- However, knowledge of "How to do it well" is delegated to `SKILL.md`.

---

## ✅ 4. Checklist

Items the agent must self-check during execution:

- [ ] **Mode Check**: Is the user intent Creation or Refinement?
- [ ] **Naming Convention**: Are prefixes like `dev-`, `obsi-` correct? (lowercase, kebab-case)
- [ ] **Path Validation**: Do paths follow the `.agent/` standard structure?
- [ ] **Template Check**: Did you use the Context-Aware Templates?
- [ ] **Validation Pass**: Did `validate_skill.py` return success?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lastdays03) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
