---
name: search-trcd
description: 3개의 증권사(키움증권, 한국투자증권, LS증권)의 API를 검색하여 해당하는 api id 즉 tr cd를 찾는다. Use when this capability is needed.
metadata:
  author: kimdoyoung
---

# search trcd(api id)

## 찾는 방법

1. backend/domains/stkcompanys/kis, ls, kiwoom/responses 폴더 하위의 모든 py를 확인한다.
2. 금용용어를 잘 해석해서 해당하는 api id 즉 tr cd를 찾는다.
3. 해당 api id 즉 tr cd를 반환한다.
4. 만약 찾았다면 해당 api id를 호출하는 test 코드를 작성한다.


## test 코드 작성

1. tools/ 폴더 하위에 작성한다.
2. 파일명 : test_{stkcompany}_{api_id}.py 로 작성한다.
3. tools/test_acct_summary.py를 참고하여 작성한다.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimdoyoung) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
