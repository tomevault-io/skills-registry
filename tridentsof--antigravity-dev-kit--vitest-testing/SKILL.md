---
name: vitest-testing
description: description: Vitest testing for Vue3 applications Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: vitest-testing
description: Vitest testing for Vue3 applications
---

# Vitest Testing

> Unit and component testing for Vue3.

---

## Basic Test

```typescript
import { describe, it, expect } from 'vitest'

describe('Calculator', () => {
  it('adds two numbers', () => {
    expect(2 + 3).toBe(5)
  })
})
```

---

## Component Test

```typescript
import { mount } from '@vue/test-utils'
import { describe, it, expect } from 'vitest'
import MyButton from './MyButton.vue'

describe('MyButton', () => {
  it('renders label', () => {
    const wrapper = mount(MyButton, {
      props: { label: 'Click me' }
    })
    expect(wrapper.text()).toContain('Click me')
  })
  
  it('emits click event', async () => {
    const wrapper = mount(MyButton)
    await wrapper.find('button').trigger('click')
    expect(wrapper.emitted('click')).toBeTruthy()
  })
})
```

---

## Testing Composables

```typescript
import { describe, it, expect } from 'vitest'
import { useCounter } from './useCounter'

describe('useCounter', () => {
  it('increments count', () => {
    const { count, increment } = useCounter()
    
    expect(count.value).toBe(0)
    increment()
    expect(count.value).toBe(1)
  })
})
```

---

## Mocking

```typescript
import { vi, describe, it, expect } from 'vitest'
import { fetchUsers } from './api'

vi.mock('./api', () => ({
  fetchUsers: vi.fn().mockResolvedValue([{ id: 1 }])
}))

describe('UserList', () => {
  it('fetches users', async () => {
    const users = await fetchUsers()
    expect(users).toHaveLength(1)
  })
})
```

---

## Pinia Store Testing

```typescript
import { setActivePinia, createPinia } from 'pinia'
import { useUserStore } from './userStore'

describe('User Store', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })
  
  it('has initial state', () => {
    const store = useUserStore()
    expect(store.user).toBeNull()
  })
})
```

---

## Run Tests

```bash
npm run test
npm run test -- --watch
npm run test -- --coverage
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
