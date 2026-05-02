---
name: azure-bicep-deploy
description: | Use when this capability is needed.
metadata:
  author: zer0big
---

# Bicep 배포 스킬(Azure)

## 수행 절차(반드시 순서대로)
0) 컨텍스트 확인
- az account show로 구독 확인, 필요 시 az account set 수행

1) 요구사항 확인
- 리소스/지역/네이밍/네트워크(publicNetworkAccess)/태그/정리 계획 확인

2) 템플릿/파라미터 준비
- 템플릿: ./templates/storage-private.bicep
- 파라미터 파일은 ARM parameter file 스키마로 작성하고 --parameters @file.json로 전달

3) 배포 전 검증
- az deployment group validate
- az deployment group what-if

4) 배포 실행
- az deployment group create -n <deploymentName>

5) 배포 후 증거 확인(필수)
- az deployment group list/show
- az resource list
- az storage account show

6) 정리
- az group delete --yes --no-wait

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zer0big) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
