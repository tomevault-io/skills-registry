---
name: riligar-dev-dashboard
description: Padrões React específicos do RiLiGar. Zustand, i18n, estrutura de arquivos, composição de componentes. Use quando construindo componentes, gerenciando estado ou implementando UI. Use when this capability is needed.
metadata:
  author: riligar
---

# Front-End — Padrões do RiLiGar

> Regras concretas baseadas na arquitetura real dos projetos. Não são genéricas — são do código que existe.

---

## Referências obrigatórias

> [!IMPORTANT]
> Sempre respeite também:
> - @[.agent/skills/riligar-design-system] — UI exclusivo via Mantine, zero CSS
> - Rules em `.agent/rules/` — clean-code, naming-conventions, code-style, javascript-only
> - [references/dependencies.md](references/dependencies.md) — Pacotes e versões do frontend, config Vite

---

## 1. Estrutura de arquivos

```
src/
├── components/          # Componentes reutilizáveis
│   ├── Sidebar.jsx      # PascalCase (SEMPRE)
│   ├── RichEditor.jsx
│   └── MediaLibrary.jsx
├── pages/               # Uma pasta por página/feature
│   ├── home.jsx         # Arquivo raiz da página: kebab-case
│   ├── editor/
│   │   └── index.jsx
│   └── feeds/
│       ├── index.jsx
│       └── FeedConfig.jsx   # Sub-componentes: PascalCase
├── store/               # Zustand stores — um arquivo por domínio
│   ├── auth-store.js
│   ├── feed-store.js
│   └── post-store.js
├── services/            # Chamadas HTTP — um arquivo por domínio
│   ├── api.js           # Instância base do cliente HTTP
│   ├── feeds.js
│   └── posts.js
├── constants/           # Constantes estáticas do app
├── i18n/                # Internacionalização
│   ├── index.js
│   └── locales/
└── hooks/               # Custom hooks compartilhados
```

### Regras de naming de arquivos

| Tipo | Convenção | Exemplo |
|---|---|---|
| Componentes (reutilizáveis) | PascalCase | `MediaLibrary.jsx` |
| Página raiz | kebab-case | `home.jsx`, `index.jsx` |
| Sub-componentes de página | PascalCase | `FeedConfig.jsx` |
| Stores | kebab-case + sufixo `-store` | `feed-store.js` |
| Services | kebab-case | `feeds.js` |
| Hooks | camelCase com prefixo `use` | `useDebounce.js` |

---

## 2. Estado — Zustand (não é opcional)

Estado global **sempre** Zustand. Sem Context para estado compartilhado. Sem Redux.

### Padrão de store

```javascript
import { create } from 'zustand'
import { feedsService } from '../services/feeds'

const useFeedStore = create((set, get) => ({
    feeds: [],
    activeFeed: null,
    isLoading: false,

    fetchFeeds: async () => {
        set({ isLoading: true })
        const feeds = await feedsService.getAll()
        set({ feeds, isLoading: false })
    },

    setActiveFeed: (feed) => set({ activeFeed: feed }),
}))
```

### Regras

- **Sem getters JavaScript** (`get isTrialing() {}`) — não são reativos no Zustand. Derive no componente ou use um selector.
- **Persistência:** use `persist` middleware apenas quando necessário (ex: `activeFeed`).
- **Sem lógica de UI** dentro do store. Stores são dados + chamadas de API.
- **Selectors granulares:** `useStore((state) => state.feeds)` — não `useStore()` inteiro.

### Errado vs Certo

```javascript
// ❌ Getter não-reativo — nunca vai atualizar o componente
const useSubscriptionStore = create((set) => ({
    subscription: null,
    get isTrialing() { return this.subscription?.status === 'trialing' }
}))

// ✅ Derive no componente
const subscription = useSubscriptionStore((s) => s.subscription)
const isTrialing = subscription?.status === 'trialing'
```

---

## 3. i18n — Todas as strings visíveis devem usar `t()`

O projeto usa **i18next** com 3 idiomas (pt-BR, en, es). Nenhuma string visível ao usuário pode ser hardcoded.

### Como usar

```javascript
import { useTranslation } from 'react-i18next'

const MyComponent = () => {
    const { t } = useTranslation()

    return (
        <Button onClick={handleSave}>{t('common.save')}</Button>
    )
}
```

### Regras

- **Sempre importar `useTranslation`** antes de usar `t()`. Sem isso, crash em runtime.
- Strings de UI: `t('namespace.key')` — nunca hardcode.
- Strings técnicas (logs, variável interna): podem ser em inglês, não precisam de `t()`.
- Chaves de namespace: domínio da feature (`feeds.`, `posts.`, `media.`) + `common.` para compartilhados.
- Mensagens de erro da API já vêm traduzidas do backend — não duplique.

### Errado

```javascript
// ❌ String hardcoded visível ao usuário
<Button>Salvar Alterações</Button>
<Text>Nenhuma mídia encontrada.</Text>

// ❌ t() sem import — crash
const handleSave = async () => {
    showNotification({ title: t('common.success') }) // ReferenceError
}
```

### Certo

```javascript
// ✅
import { useTranslation } from 'react-i18next'

const { t } = useTranslation()
<Button>{t('common.save')}</Button>
<Text>{t('media.empty')}</Text>
```

---

## 4. Services — camada de HTTP

Todas as chamadas de API vão por `services/`. Componentes e stores não chamam HTTP diretamente.

### Padrão

```javascript
// services/feeds.js
import { api } from './api'

export const feedsService = {
    getAll: () => api.get('feeds').json(),
    getById: (id) => api.get(`feeds/${id}`).json(),
    create: (data) => api.post('feeds', { json: data }).json(),
    update: (id, data) => api.put(`feeds/${id}`, { json: data }).json(),
    remove: (id) => api.delete(`feeds/${id}`).json(),
}
```

- `api.js` tem a instância base com token de auth e tratamento de erro.
- **Não duplique `API_URL`** — já está no `api.js`. Nunca redefina em outro arquivo.
- Services são funções puras de chamada HTTP. Sem lógica de estado.

---

## 5. Componentes — regras práticas

### Composição

- Um componente, uma responsabilidade.
- Se um componente ultrapassar ~100 linhas, divide.
- Props down, events up. Sem drilling profundo — usa store.

### Interação com usuário

- **Confirmação de exclusão:** sempre usar `HoldButton` / `ButtonDelete` do `components/Buttons.jsx`. Nunca usar `confirm()` nativo.
- **Hover/focus:** usar props do Mantine (`styles`, `withBorder`, hover via `&:hover` no `styles`). Nunca manipular DOM diretamente via `e.currentTarget.style`.

```javascript
// ❌ Manipulação direta de DOM
onMouseEnter={(e) => e.currentTarget.style.background = '#eee'}

// ✅ Mantine styles prop
styles={{ root: { '&:hover': { backgroundColor: 'var(--mantine-color-dimmed)' } } }}
```

### Não redefina componentes do Mantine

Se você precisa de um `Center`, `TextInput`, `Button` — use o do Mantine. Nunca crie um local com o mesmo nome, isso gera shadow e confusão.

---

## 6. Constantes e valores fixos

- Strings de config (phone numbers, mensagens template, URLs externas) vão em `constants/` ou `.env`.
- Nunca hardcode dentro de componentes.

```javascript
// ❌
const message = `Olá, preciso de ajuda com meu plano.`

// ✅ constants/whatsapp.js
export const WHATSAPP_SUPPORT_NUMBER = '...'
export const WHATSAPP_SUPPORT_MESSAGE = '...' // ou via i18n se traduzível
```

---

## 7. Anti-patterns (do código real)

| ❌ Problema real encontrado | ✅ Como resolver |
|---|---|
| `slugify` duplicado em 2 arquivos | Extrair para `utils/slugify.js` |
| `stripHtml` duplicado em 2 arquivos | Extrair para `utils/stripHtml.js` |
| `API_URL` redefinido fora do `api.js` | Importar do `services/api.js` |
| `t()` usado sem `useTranslation` importado | Sempre verificar import |
| Componente Mantine shadowed por local | Deletar o local, usar Mantine |
| Código comentado espalhado | Deletar. Se precisa, usa git. |
| `confirm()` misturado com HoldButton | Usa HoldButton sempre |
| `<style>` tag com CSS raw | Usa `styles` prop do Mantine (exceção: libs externas como ProseMirror que precisam de CSS global) |
| Getters no Zustand store | Derive no componente |
| `onMouseEnter` manipulando style diretamente | Usa `styles` prop do Mantine |

---

## 8. Padrões reutilizáveis

Estruturas que repetem pela codebase. Copie o esqueleto, ajuste apenas o conteúdo domínio-específico.

### 8.1 Page Header

Presente em **todas** as pages. Estrutura idêntica sempre:

```javascript
<Box py="xl">
    <Group justify="space-between" align="flex-end" mb="xl">
        <Stack gap={0}>
            <Text size="xs" fw={700} c="dimmed" tt="uppercase" lts="0.1em">
                {t('namespace.subtitle')}
            </Text>
            <Title order={1} style={{ letterSpacing: '-0.04em' }}>
                {t('namespace.title')}
            </Title>
        </Stack>
        {/* CTA opcional — ex: <Button leftSection={<IconPlus size={16} />}> */}
    </Group>
    {/* Conteúdo da página */}
</Box>
```

### 8.2 Empty State

Usado quando uma lista está vazia. Card com borda dashed, icone grande, texto e CTA:

```javascript
<Card padding="xl" radius="md" withBorder style={{ borderStyle: 'dashed', textAlign: 'center' }}>
    <Stack align="center" py="xl">
        <IconDominio size={48} stroke={1} color="var(--mantine-color-gray-2)" />
        <Text c="dimmed" size="sm">{t('namespace.emptyMessage')}</Text>
        <Button onClick={handleCreate} leftSection={<IconPlus size={16} />}>
            {t('namespace.createFirst')}
        </Button>
    </Stack>
</Card>
```

### 8.3 Loading Guard

Loader só aparece quando não há dados ainda (não sobrescreve lista existente):

```javascript
{loading && data.length === 0 ? (
    <Center style={{ height: 300 }}>
        <Loader />
    </Center>
) : (
    /* conteúdo normal */
)}
```

### 8.4 Card Grid

Layout responsivo padrão para listas de cards:

```javascript
<SimpleGrid cols={{ base: 1, sm: 2, md: 3 }}>
    {items.map((item) => (
        <Card key={item.id} padding="lg" radius="md" withBorder>
            {/* conteúdo do card */}
        </Card>
    ))}
</SimpleGrid>
```

Para galerias (mais itens pequenos): `cols={{ base: 1, sm: 2, md: 3, lg: 4 }}`

### 8.5 Modal

Sempre usa `useDisclosure`. Header com borderBottom específico:

```javascript
const [opened, { open, close }] = useDisclosure(false)

<Modal
    opened={opened}
    onClose={close}
    centered
    radius="md"
    padding="xl"
    title={<Text fw={700}>{t('namespace.modalTitle')}</Text>}
    styles={{ header: { borderBottom: '1px solid var(--mantine-color-gray-2)', marginBottom: 20 } }}
>
    {/* corpo do modal */}
</Modal>
```

Para múltiplos modais na mesma page, renomeia as funções: `{ open: openEdit, close: closeEdit }`

### 8.6 Status Badge

Mapeia status → configuração visual via função que recebe `t`:

```javascript
const getStatusConfig = (t) => ({
    draft:     { color: 'gray',   icon: <IconCircleDotted size={16} />, label: t('posts.status.draft') },
    scheduled: { color: 'blue',   icon: <IconClock size={16} />,        label: t('posts.status.scheduled') },
    published: { color: 'green',  icon: <IconCircleCheck size={16} />,  label: t('posts.status.published') },
    failed:    { color: 'red',    icon: <IconCircleX size={16} />,      label: t('posts.status.failed') },
})

// Uso
const config = getStatusConfig(t)[status]
<Badge variant="dot" color={config.color}>{config.label}</Badge>
```

### 8.7 Search / Filter

Filtro local sem chamada de API — estado local + filter inline:

```javascript
const [search, setSearch] = useState('')

const filtered = items.filter((item) =>
    item.name.toLowerCase().includes(search.toLowerCase())
)

<TextInput
    placeholder={t('common.search')}
    leftSection={<IconSearch size={16} />}
    value={search}
    onChange={(e) => setSearch(e.target.value)}
/>
```

---

## 9. Padrões de dados e lógica

### 9.1 Store — async action

Template exato que todas as actions seguem. Imports do service como namespace:

```javascript
import { create } from 'zustand'
import * as feedsService from '../services/feeds.js'

export const useFeedStore = create((set) => ({
    feeds: [],
    loading: false,
    error: null,

    fetchFeeds: async () => {
        set({ loading: true, error: null })
        try {
            const feeds = await feedsService.getAll()
            set({ feeds, loading: false })
        } catch (error) {
            set({ error: error.message, loading: false })
            throw error
        }
    },
}))
```

Nota: services são importados como `import * as service` (namespace), não como objeto exportado.

### 9.2 Store — mutação de listas

Atualizações imutáveis via `set(state => ...)` com spread + map/filter:

```javascript
// Atualizar item na lista
updateFeed: (id, data) => set((state) => ({
    feeds: state.feeds.map((f) => (f.id === id ? { ...f, ...data } : f))
})),

// Remover item
removeFeed: (id) => set((state) => ({
    feeds: state.feeds.filter((f) => f.id !== id)
})),

// Adicionar item
addFeed: (feed) => set((state) => ({
    feeds: [...state.feeds, feed]
})),
```

### 9.3 Notifications

Shape e convenção de cores consistente em toda a app:

```javascript
import { notifications } from '@mantine/notifications'
import { IconCheck, IconX, IconAlertCircle } from '@tabler/icons-react'

// ✅ Sucesso
notifications.show({
    title: t('common.success'),
    message: t('namespace.savedMessage'),
    color: 'green',
    icon: <IconCheck size={18} />,
})

// ✅ Erro
notifications.show({
    title: t('common.error'),
    message: error.message,
    color: 'red',
    icon: <IconX size={18} />,
})

// ✅ Warning
notifications.show({
    title: t('common.warning'),
    message: t('namespace.warningMessage'),
    color: 'yellow',
    icon: <IconAlertCircle size={18} />,
})
```

Icones de notification: sempre `size={18}`. Mensagens de erro: usa `error.message` diretamente (já vem traduzido do backend).

### 9.4 dayjs + i18n

Locale do dayjs sincroniza com o idioma do i18n:

```javascript
import dayjs from 'dayjs'
import relativeTime from 'dayjs/plugin/relativeTime'
import { useTranslation } from 'react-i18next'

dayjs.extend(relativeTime)

const MyComponent = () => {
    const { i18n } = useTranslation()

    useEffect(() => {
        dayjs.locale(i18n.language)
    }, [i18n.language])

    // Formatos usados no projeto:
    // dayjs(date).format('DD MMM, HH:mm')       — compacto com hora
    // dayjs(date).format('DD/MM/YYYY [at] HH:mm') — completo
    // dayjs(date).fromNow()                      — relativo ("há 2 dias")
}
```

---

## 10. Padrões de fluxo

### 10.1 Route Guard (wrapper)

Componente que protege rotas verificando estado do store:

```javascript
import { Navigate } from 'react-router-dom'
import { useFeedStore } from '../store/feed-store.js'

const RequireFeed = ({ children }) => {
    const activeFeed = useFeedStore((s) => s.activeFeed)
    if (!activeFeed) return <Navigate to="/" />
    return children
}
```

Usado na definição de rotas: `<RequireFeed><EditorPage /></RequireFeed>`

### 10.2 Notificação via URL params

Após redirects externos (OAuth, Stripe checkout), status vem via query params:

```javascript
import { useSearchParams } from 'react-router-dom'

const SubscriptionPage = () => {
    const [searchParams, setSearchParams] = useSearchParams()
    const { t } = useTranslation()

    useEffect(() => {
        if (searchParams.get('success')) {
            notifications.show({ title: t('common.success'), message: t('subscription.successMessage'), color: 'green', icon: <IconCheck size={18} /> })
            setSearchParams({})
        } else if (searchParams.get('canceled')) {
            notifications.show({ title: t('common.warning'), message: t('subscription.canceledMessage'), color: 'yellow', icon: <IconAlertCircle size={18} /> })
            setSearchParams({})
        }
    }, [searchParams])
}
```

### 10.3 Autosave

Padrão usado no editor — debounce com state machine de status:

```javascript
const [saveStatus, setSaveStatus] = useState('idle') // 'idle' | 'saving' | 'saved'

useEffect(() => {
    if (!content) return
    const timeout = setTimeout(async () => {
        setSaveStatus('saving')
        try {
            await postsService.update(postId, { content })
            setSaveStatus('saved')
            // Reset para idle após 3s
            setTimeout(() => setSaveStatus('idle'), 3000)
        } catch {
            setSaveStatus('idle')
        }
    }, 2000) // debounce de 2s

    return () => clearTimeout(timeout)
}, [content, postId])
```

---

## 11. Convenção de tamanhos de icones

Hierarquia consistente — sempre de `@tabler/icons-react`:

| Contexto | Size | Exemplo |
|---|---|---|
| Menu items / nav | 14 | Sidebar links |
| Inline / badges | 16 | Botões, labels, leftSection |
| Notifications | 18 | Icons nas notifications |
| Card headers | 20 | Ação principal do card |
| Feature cards | 24 | Cards de destaque |
| Empty states | 48 | Icone do empty state (com `stroke={1}`) |

Empty states usam `stroke={1}` para parecer mais leve. Icones decorativos genéricos usam `stroke={1.5}`.

---

## Related Skills

- @[.agent/skills/riligar-design-system]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riligar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
