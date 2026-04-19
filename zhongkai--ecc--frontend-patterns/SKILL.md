---
name: frontend-patterns
description: React 和 Next.js 前端开发模式。包含组件设计、状态管理、性能优化等最佳实践。 Use when this capability is needed.
metadata:
  author: zhongkai
---

# 前端模式技能

React 和 Next.js 开发的最佳实践和模式。

## 组件设计

### 组件分类

#### 展示组件
只负责 UI，不包含业务逻辑

```tsx
function UserCard({ name, avatar, role }: UserCardProps) {
  return (
    <div className="user-card">
      <img src={avatar} alt={name} />
      <h3>{name}</h3>
      <span>{role}</span>
    </div>
  )
}
```

#### 容器组件
处理数据获取和业务逻辑

```tsx
function UserCardContainer({ userId }: { userId: string }) {
  const { data: user, isLoading } = useUser(userId)
  
  if (isLoading) return <Skeleton />
  if (!user) return <NotFound />
  
  return <UserCard {...user} />
}
```

### 组件命名
- 使用 PascalCase
- 描述性名称
- 动词+名词组合

```
CreateUserForm
UserProfileCard
NavigationMenu
```

## Hooks 模式

### 自定义 Hooks
提取可重用逻辑

```tsx
function useUser(userId: string) {
  const [user, setUser] = useState<User | null>(null)
  const [isLoading, setIsLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)
  
  useEffect(() => {
    fetchUser(userId)
      .then(setUser)
      .catch(setError)
      .finally(() => setIsLoading(false))
  }, [userId])
  
  return { user, isLoading, error }
}
```

### Hooks 规则
- 只在函数组件顶层调用
- 不在条件/循环中调用
- 依赖数组要完整

## 状态管理

### 本地状态
简单状态使用 useState

### 复杂状态
使用 useReducer

```tsx
function userReducer(state: State, action: Action): State {
  switch (action.type) {
    case 'SET_USER':
      return { ...state, user: action.payload }
    case 'SET_LOADING':
      return { ...state, isLoading: action.payload }
    default:
      return state
  }
}
```

### 全局状态
- Context API（简单场景）
- Zustand/Jotai（复杂场景）

## 性能优化

### 避免不必要的渲染
```tsx
// 使用 memo 缓存组件
const MemoizedComponent = memo(ExpensiveComponent)

// 使用 useMemo 缓存计算
const expensiveValue = useMemo(() => compute(deps), [deps])

// 使用 useCallback 缓存函数
const handleClick = useCallback(() => {}, [deps])
```

### 代码分割
```tsx
const LazyComponent = lazy(() => import('./HeavyComponent'))

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <LazyComponent />
    </Suspense>
  )
}
```

## Next.js 模式

### 数据获取
```tsx
// Server Component
async function UserPage({ params }: { params: { id: string } }) {
  const user = await getUser(params.id)
  return <UserProfile user={user} />
}
```

### API 路由
```typescript
// app/api/users/route.ts
export async function GET(request: Request) {
  const users = await getUsers()
  return Response.json({ data: users })
}
```

## 表单处理

### 使用 React Hook Form
```tsx
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'

function CreateUserForm() {
  const { register, handleSubmit, errors } = useForm({
    resolver: zodResolver(userSchema)
  })
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} />
      {errors.name && <span>{errors.name.message}</span>}
    </form>
  )
}
```

## 错误边界

```tsx
class ErrorBoundary extends Component {
  state = { hasError: false }
  
  static getDerivedStateFromError() {
    return { hasError: true }
  }
  
  render() {
    if (this.state.hasError) {
      return <ErrorFallback />
    }
    return this.props.children
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhongkai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
