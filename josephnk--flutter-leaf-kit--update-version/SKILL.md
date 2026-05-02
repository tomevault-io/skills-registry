---
name: update-version
description: 모든 패키지의 pubspec.yaml 버전을 일괄 업데이트합니다. 사용법: /update-version [버전] (예: 2.5.0-dev) Use when this capability is needed.
metadata:
  author: josephnk
---

## 버전 업데이트

인자로 받은 버전으로 패키지 버전을 업데이트합니다.

## 인자

**$ARGUMENTS**

## 실행 규칙

1. 인자가 없으면 버전을 입력하라고 안내하세요
2. 인자가 있으면 바로 명령어를 실행하세요: `poetry run update-version $ARGUMENTS --auto-commit`
3. 실행 결과를 사용자에게 보여주세요

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/josephnk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
