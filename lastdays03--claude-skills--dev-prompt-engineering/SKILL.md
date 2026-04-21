---
name: dev-prompt-engineering
description: Anthropic의 Best Practice를 기반으로 고성능 프롬프트를 설계하고 최적화합니다. Use when this capability is needed.
metadata:
  author: lastdays03
---

# 🎨 프롬프트 엔지니어링 (Dev Prompt Engineering)

이 워크플로우는 `anthropics/prompt-eng-interactive-tutorial`의 원칙을 사용하여 최적의 Claude 프롬프트를 작성합니다.

## 1. 초기화 (Initialization)
1.  **스킬 로드**: `this document`를 읽어 "Anthropic Best Practices"를 파악합니다.
2.  **목표 설정**: 사용자에게 다음 세 가지를 묻습니다.
    *   **Role**: 에이전트가 어떤 페르소나를 가져야 합니까?
    *   **Task**: 수행해야 할 핵심 작업은 무엇입니까?
    *   **Constraint**: 출력 형식(JSON, XML 등)이나 제약 사항은 무엇입니까?
3.  **Language Check**: 다음 규칙을 사용자에게 인지시킵니다.
    *   **프롬프트 본문**: 모델 성능 극대화를 위해 **영어**로 작성됩니다.
    *   **최종 답변**: 사용자가 읽기 편하도록 **한국어**로 출력되도록 설정합니다.

## 2. 초안 작성 (Drafting - Context & Role)
**"Context First, Instructions Later"**

1.  **템플릿 로드**: `resources/prompt-template.md`를 로드합니다.
2.  **슬롯 채우기 (Slot Filling)**: 사용자 입력 정보를 바탕으로 템플릿의 `{{ }}` 플레이스홀더를 채웁니다.
    *   `{{DOMAIN}}`: Role 정보
    *   `{{GOAL}}`: Task 정보
    *   `{{YEARS}}`: (Optional) 경력 연차 (기본값: Senior/10+)
3.  **Draft V1 (Strict Adherence)**:
    - **구조 유지**: `prompt-template.md`의 모든 XML 태그(`<system_role>`, `<context_and_data>` 등)와 내용을 **그대로 유지**해야 합니다.
    - **작업 내용**: 오직 `{{ }}`로 감싸진 플레이스홀더만 사용자의 입력으로 교체합니다. 임의로 섹션을 생략하거나 요약하지 마십시오.
    - 작성된 전체 프롬프트를 사용자에게 제시합니다.

## 3. 고도화 (Refining - CoT & Few-Shot)
**"생각하게 만들고, 예시를 보여주세요."**

1.  **예시 추가 (Few-Shot)**: 사용자가 원하는 이상적인 입출력 예시를 2~3개 추가합니다.
2.  **CoT 적용**: 복잡한 작업인 경우, `<thinking>` 태그를 사용하여 단계별로 추론하도록 유도합니다. ("Think step-by-step")
3.  **Draft V2**: 개선된 프롬프트를 작성합니다.

## 4. 검증 및 최적화 (Verification)
1.  **시뮬레이션**: 작성된 프롬프트를 에이전트 스스로 평가해봅니다. (Self-Correction)
2.  **Edge Case 점검**: "모르겠으면 모른다고 말해" 등의 환각(Hallucination) 방지 문구가 있는지 확인합니다.
3.  **완료**: 최종 프롬프트를 제공합니다.


---

## Standards & Rules

# Prompt Engineering (Dev Prompt Engineering)

## Core Principles (Anthropic Best Practices)

### 1. The "Context-First" Rule
- **Context**: Always provide relevant context *before* the instruction.
- **Role**: Assign a persona (e.g., "You are an expert Python architect").
- **XML Tags**: Use XML tags (e.g., `<documents>`, `<instruction>`) to structure input. Claude loves XML.

### 2. The Power of Examples (Few-Shot)
- **Show, Don't Just Tell**.
- Provide 3+ examples of "Input -> Ideal Output" to guide style and format.
- **Anti-Hallucination**: Include examples of how to say "I don't know" or handle edge cases.

### 3. Precognition (Chain of Thought)
- **Let Claude Think**: For complex tasks, ask Claude to "Think step-by-step" before answering.
- **Thinking Tags**: Use `<thinking>` blocks to verify logic before generating the final `<answer>`.

### 4. Language Strategy (Performance vs Usability)
- **Prompt Language**: **English**. (LLMs reason better in English). All instructions, constraints, and system prompts must be in English.
- **Output Language**: **Korean**. The final response meant for the user must be in Korean.
- **Rule**: "Think in English, Speak in Korean."

## 🏗️ Structure of a Great Prompt

1.  **Role & Goal**: Who is Claude? What is the objective?
2.  **Context/Data**: Reference materials wrapped in XML.
3.  **Rules & Constraints**: Dos and Don'ts.
4.  **Examples (Few-Shot)**: Golden samples.
5.  **Instruction**: The immediate task.
6.  **Pre-computation**: "Take a deep breath and think step by step..."

## ✅ Quality Standards
- **Clarity**: Unambiguous instructions.
- **Separation**: Data and instructions are visually distinct (XML).
- **Iterative**: Every prompt should be tested and refined.

## Checklist
- [ ] **Persona**: Is a specific role assigned?
- [ ] **XML Structuring**: Are data parts wrapped in tags?
- [ ] **Examples**: Are there at least 2-3 examples?
- [ ] **CoT**: Is identifying the reasoning process (Thinking) required?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lastdays03) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
