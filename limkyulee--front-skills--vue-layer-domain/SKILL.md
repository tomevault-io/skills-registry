---
name: vue-layer-domain
description: Vue3 프로젝트에 새로운 도메인 페이지를 추가하는 스킬. 도메인 이름을 받아 src/pages/[domain]/ 폴더에 고정 4개 파일(UserIndexPage/UserDetailPage/UserFormPage)을 생성하고 Vue Router 4 공식 권고 방식(children + named routes + props)으로 라우트를 자동 등록한다. 사용자가 '도메인 추가해줘', '페이지 만들어줘', 'user 도메인 생성해줘', '새 페이지 추가', '라우트 추가해줘', 'order 도메인 폴더 만들어줘' 같은 말을 할 때 이 스킬을 사용할 것. 도메인이 늘어날 때마다 반복 실행한다. Use when this capability is needed.
metadata:
  author: limkyulee
---

# Vue Layer — Domain (페이지 스캐폴더)

도메인 이름을 받아 페이지 파일을 생성하고 Vue Router 4 공식 방식으로 라우터에 등록한다.
위젯/피처/엔티티는 생성하지 않는다 — 개발자가 직접 채운다.

---

## 입력 컨텍스트 확인

```
PROJECT_PATH  : 프로젝트 루트 경로
DOMAIN_NAME   : 도메인 이름 소문자 (예: user, order, product)
REQUIRES_AUTH : 인증 필요 여부 (기본값: true)
LAYOUT        : 적용할 레이아웃 (기본값: 'default')
```

DOMAIN_NAME이 없으면 반드시 먼저 물어본다.
나머지는 기본값이 있으므로 묻지 않아도 된다.

---

## 패키지 설치 체크

```bash
cd [PROJECT_PATH]
# vue-router 설치 여부 확인 (라우터 등록에 필요)
if ! node -e "require('vue-router')" 2>/dev/null; then
  [PKG_MANAGER] add vue-router@^4.4.0
fi
```

네이밍 변환 규칙:
```
DOMAIN_NAME = user
  → 파일 prefix  : User       (PascalCase)
  → 라우트 path  : /user      (kebab-case)
  → 라우트 name  : user-*     (kebab-case prefix)
  → CSS class    : user-*     (kebab-case)
```

---

## 생성 결과 구조

```
src/pages/[domain]/
├── [Domain]IndexPage.vue   — 목록 페이지
├── [Domain]DetailPage.vue  — 상세/단건 조회 페이지
└── [Domain]FormPage.vue    — 등록(create) + 수정(edit) 공용 폼 페이지
```

예시 — DOMAIN_NAME=user
```
src/pages/user/
├── UserIndexPage.vue
├── UserDetailPage.vue
└── UserFormPage.vue
```

---

## 생성 파일 내용 기준

### [Domain]IndexPage.vue — 목록 페이지
```vue
<script setup lang="ts">
/**
 * [Domain] 목록 페이지
 *
 * ✅ 허용: features composable 호출, widgets/entities 컴포넌트 조립
 * ❌ 금지: API 직접 호출, 비즈니스 로직 작성
 */
</script>

<template>
  <div class="[domain]-index-page">
    <!-- TODO: 목록 UI 조립 -->
    <!-- 예: <[Domain]Table />, <[Domain]Filter /> -->
  </div>
</template>

<style scoped>
.[domain]-index-page {
  padding: var(--spacing-6);
}
</style>
```

### [Domain]DetailPage.vue — 상세 페이지
```vue
<script setup lang="ts">
/**
 * [Domain] 상세 페이지
 *
 * props.id — Vue Router props: true 로 자동 주입 (useRoute() 직접 사용 금지)
 * ✅ 허용: features composable 호출, widgets/entities 컴포넌트 조립
 * ❌ 금지: API 직접 호출, useRoute()로 params 직접 접근
 */

const props = defineProps<{
  id: string
}>()
</script>

<template>
  <div class="[domain]-detail-page">
    <!-- TODO: 상세 UI 조립 -->
    <!-- props.id 사용: {{ props.id }} -->
  </div>
</template>

<style scoped>
.[domain]-detail-page {
  padding: var(--spacing-6);
}
</style>
```

### [Domain]FormPage.vue — 등록/수정 공용 폼 페이지
```vue
<script setup lang="ts">
/**
 * [Domain] 등록/수정 폼 페이지
 *
 * props.id 없음 → 등록 모드 (route name: [domain]-create)
 * props.id 있음 → 수정 모드 (route name: [domain]-edit)
 *
 * ✅ 허용: features composable 호출, BaseField + BaseInput 조합
 * ❌ 금지: API 직접 호출
 */

const props = defineProps<{
  id?: string   // 수정 모드일 때만 주입됨
}>()

const isEdit = computed(() => !!props.id)
</script>

<template>
  <div class="[domain]-form-page">
    <!-- TODO: 폼 UI 조립 -->
    <!-- 예: <BaseField label="이름"><BaseInput v-model="form.name" /></BaseField> -->
    <!-- 예: <BaseButton variant="primary" @click="submit">{{ isEdit ? '수정' : '저장' }}</BaseButton> -->
  </div>
</template>

<style scoped>
.[domain]-form-page {
  padding: var(--spacing-6);
}
</style>
```

---

## 라우터 등록 규칙 (Vue Router 4 공식 권고 방식)

src/router/index.ts의 routes 배열 끝에 **children 블록** 단위로 추가한다.
기존 내용은 건드리지 않는다.

### 등록 패턴

```ts
// ✅ Vue Router 4 공식 방식: children + named routes + props: true
{
  path: '/[domain]',
  children: [
    {
      path: '',
      name: '[domain]-list',
      component: () => import('@/pages/[domain]/[Domain]IndexPage.vue'),
      meta: { requiresAuth: true, layout: 'default', title: '[Domain] 목록' }
    },
    {
      path: 'create',
      name: '[domain]-create',
      component: () => import('@/pages/[domain]/[Domain]FormPage.vue'),
      meta: { requiresAuth: true, layout: 'default', title: '[Domain] 등록' }
    },
    {
      path: ':id',
      name: '[domain]-detail',
      component: () => import('@/pages/[domain]/[Domain]DetailPage.vue'),
      props: true,  // ← id를 props로 자동 전달
      meta: { requiresAuth: true, layout: 'default', title: '[Domain] 상세' }
    },
    {
      path: ':id/edit',
      name: '[domain]-edit',
      component: () => import('@/pages/[domain]/[Domain]FormPage.vue'),
      props: true,  // ← id를 props로 자동 전달
      meta: { requiresAuth: true, layout: 'default', title: '[Domain] 수정' }
    }
  ]
},
```

### children 구조의 이점

1. **경로 충돌 자동 해결** — `create`(정적)가 `:id`(동적)보다 항상 우선 매칭
2. **Named routes로 이동** — 경로 문자열 하드코딩 없음
   ```ts
   // ❌ 하드코딩
   router.push('/user/123')

   // ✅ named route
   router.push({ name: 'user-detail', params: { id: '123' } })
   ```
3. **props: true** — 컴포넌트가 라우터에 직접 의존하지 않아 테스트 용이

---

## 반복 실행

도메인이 추가될 때마다 반복 실행한다.

```
1회차: DOMAIN_NAME=user    → src/pages/user/ 3개 파일 + children 블록
2회차: DOMAIN_NAME=order   → src/pages/order/ 3개 파일 + children 블록
3회차: DOMAIN_NAME=product → src/pages/product/ 3개 파일 + children 블록
```

각 실행은 독립적이며 기존 파일을 건드리지 않는다.

---

## 완료 확인

```
✅ [domain] user 도메인 생성 완료

📁 생성된 파일:
   src/pages/user/UserIndexPage.vue
   src/pages/user/UserDetailPage.vue
   src/pages/user/UserFormPage.vue

🔗 등록된 라우트 (named routes):
   user-list   →  GET /user
   user-create →  GET /user/create
   user-detail →  GET /user/:id        (props.id 자동 주입)
   user-edit   →  GET /user/:id/edit   (props.id 자동 주입)

💡 이동 예시:
   router.push({ name: 'user-list' })
   router.push({ name: 'user-detail', params: { id: '123' } })
   router.push({ name: 'user-edit',   params: { id: '123' } })

📋 다음 단계:
   - src/features/user/ 에 composable 작성
   - src/widgets/ 에 user 전용 UI 블록 작성
   - src/entities/user/types.ts 에 도메인 타입 정의
```

---
> Source: [limkyulee/front-skills](https://github.com/limkyulee/front-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
