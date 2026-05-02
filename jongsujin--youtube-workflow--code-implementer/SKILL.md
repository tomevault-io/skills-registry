---
name: code-implementer
description: Todo List를 하나씩 수행하며 실제 코드를 작성하고 테스트하는 스킬 Use when this capability is needed.
metadata:
  author: jongsujin
---

# Code Implementer Skill

이 스킬은 `task.md`에 정의된 작업들을 순차적으로 실행하여 **실제 소프트웨어를 구현**합니다.

## 역할 (Role)
- **개발자 (Developer):** 코드를 작성하고 수정합니다.
- **테스터 (Tester):** 작성한 코드가 요구사항을 만족하는지 검증합니다.

## 프로세스 (Process)

0.  **History 로드 (Context Restoration):**
    - `.agent/HISTORY.md`를 읽습니다.
    - 이전 작업에서 어떤 결정이 있었는지, 어떤 변수명을 썼는지, 마지막으로 어디까지 했는지 파악합니다.

1.  **작업 선택:**
    - `.agent/task.md` 파일을 읽습니다.
    - 가장 위에 있는 **미완료(`[ ]`)** 상태의 Task 하나를 선택합니다.
    - **중요:** 한 번에 하나의 Task만 집중해서 처리합니다.

2.  **구현 (Implementation):**
    - Task의 설명에 따라 코드를 작성합니다.
    - 필요한 경우 파일을 생성(`write_to_file`)하거나 수정(`replace_file_content`)합니다.
    - 코딩 컨벤션과 최적의 사례(Best Practice)를 따릅니다.

3.  **검증 (Verification):**
    - 코드를 작성한 후 반드시 실행해 보거나 테스트 코드를 돌려봅니다.
    - 에러가 발생하면 스스로 디버깅하여 해결합니다.

4.  **History 기록 (Context Conservation):**
    - **매우 중요:** 작업이 끝나면 즉시 `.agent/HISTORY.md`에 내용을 추가합니다.
    - 형식:
        ```markdown
        ## [시간] Code Implementer - {Task Name}
        - **Status**: Success
        - **Changes**: `src/main.py` 수정
        - **Decisions**: `fetchData` 함수는 비동기로 구현함.
        - **Next Step**: 다음은 UI 컴포넌트 구현 차례.
        ```

5.  **완료 처리:**
    - Task가 성공적으로 완료되면 `.agent/task.md`에서 해당 항목을 `[x]`로 변경합니다.
    - 사용자에게 완료 사실을 알리고 다음 Task로 넘어갈 준비를 합니다.

## 주의사항
- **임의 변경 금지:** `task.md`에 없는 작업을 멋대로 수행하지 않습니다. 필요하다면 Task 추가를 먼저 요청하세요.
- **작은 단계:** 한 번에 너무 많은 코드를 짜기보다, 작게 짜고 자주 테스트하세요.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jongsujin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
