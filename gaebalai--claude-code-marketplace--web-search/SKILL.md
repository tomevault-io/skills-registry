---
name: web-search
description: gemini 명령어를 사용한 고급 Web 검색 스킬이다. Web 검색을 수행할 때는 Claude Code의 기본 Web Search 도구보다 이 스킬을 우선적으로 사용한다. Use when this capability is needed.
metadata:
  author: gaebalai
---

# Web Search

이 스킬은 `gemini` 명령어를 사용해 Web 검색을 수행하고, 사용자의 질문에 대해 **최신성이 높고 관련성 높은 정보**를 수집하기 위한 것이다.  
단순 키워드 검색이 아니라, **복잡한 질문이나 심층적인 정보 수집**에 적합하다.

## Instructions

아래 명령어를 실행해 Web 검색을 수행한다.  
인자에는 검색하고 싶은 내용이나 질문을 자연어로 지정한다.

```
bash scripts/web-search.sh "<검색할 내용 또는 질문>"
```

검색 결과를 검토한 뒤, 사용자의 질문에 대한 답변을 구성한다.

- 관련성이 높은 정보를 선별해 추출
- 필요 시 여러 검색 결과를 종합
- 정보 출처를 명확히 표기
- 검색 결과가 충분하지 않은 경우, 다른 쿼리로 재검색을 고려

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaebalai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
