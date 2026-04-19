---
name: frontend-analysis
description: Análise e implementação de código frontend Angular/TypeScript. Use para: criar componentes, serviços, diretivas, pipes, ajustar UI, refatorar código Angular, implementar signals, forms reativos, state management, DI, estilização, acessibilidade e responsividade. Triggers: 'implementar tela', 'criar componente', 'ajustar layout', 'corrigir bug frontend', 'refatorar'. Use when this capability is needed.
metadata:
  author: guilherme-xmatch
---

# Frontend Analysis Skill — Angular/TypeScript

Skill para análise e implementação de código frontend de alta qualidade com Angular 17+ e TypeScript 5+.

## Quando Usar
- Criar, ajustar ou refatorar componentes, serviços, diretivas e pipes Angular.
- Implementar telas a partir de especificações (Figma ou textuais).
- Resolver bugs de frontend (layout, estados, interações, change detection).
- Refatorar código existente para melhor qualidade, performance ou patterns modernos.
- Implementar state management (Signals, NgRx, services reativos).
- Criar ou ajustar formulários reativos e validadores.

## Checklist de Implementação

### Antes de Codar
1. Verificar se existem componentes e serviços reutilizáveis no projeto.
2. Consultar design system / tokens de design existentes.
3. Entender a estrutura de pastas e convenções de nomenclatura.
4. Identificar dependências (libs, módulos shared, services, DI tokens).
5. **Aplicar Decision Trees** (DT-1 a DT-6 do agent) para cada unidade.

### Durante a Implementação
1. **Componentização**: Smart (container) vs Presentational (dumb), standalone.
2. **Tipagem**: interfaces TypeScript strict, zero `any`, generics quando aplicável.
3. **Change Detection**: `OnPush` em todos os componentes (documentar exceções).
4. **Signals/RxJS**: signal() para local state, Observable para async streams.
5. **DI**: services `providedIn: 'root'`, inject() function, InjectionTokens.
6. **Estados**: loading, erro, vazio, sucesso — todos tratados (`@if/@else`, `@defer`).
7. **Acessibilidade**: semântica HTML, ARIA, CDK a11y, keyboard nav, contraste.
8. **Responsividade**: mobile-first, breakpoints do design system.
9. **Performance**: OnPush, track em @for, @defer, computed() vs getters, pipes puros.
10. **Subscriptions**: takeUntilDestroyed/async pipe/toSignal — NUNCA subscription sem cleanup.

### Após Codar
1. Revisar código contra padrões do projeto e Production Checklist.
2. Sugerir testes (unitários com TestBed, marble testing para RxJS, fixtures).
3. Documentar decisões não óbvias (TSDoc para APIs públicas).
4. Verificar que não há `any`, subscriptions sem cleanup, ou OnPush faltante.

## Padrões de Código

### Componentes Angular
- Standalone components (padrão em Angular 17+).
- Nomear com PascalCase, seletor com prefixo `app-` (kebab-case).
- `ChangeDetectionStrategy.OnPush` como padrão.
- `input()` / `output()` signal-based (Angular 17+) sobre `@Input` / `@Output`.
- Novo control flow: `@if`, `@for`, `@switch`, `@defer` sobre diretivas estruturais.
- Exportar interfaces/types junto ao componente ou em arquivo `.models.ts`.

### Services
- `@Injectable({ providedIn: 'root' })` para singletons.
- Feature-scoped services via `providers: []` do componente ou rota.
- Signals para estado reativo (`signal()`, `computed()`).
- Observables com sufixo `$` para streams assíncronos.
- HttpInterceptorFn para cross-cutting concerns.

### Formulários
- Reactive Forms (`FormGroup`/`FormControl`/`FormArray`) como padrão.
- `NonNullableFormBuilder` para type safety.
- `ControlValueAccessor` para custom form controls reutilizáveis.
- Async validators com debounce para server-side validation.

### Estilos
- Seguir a abordagem do projeto (SCSS, Tailwind, Angular Material, etc.).
- Usar tokens de design system quando disponíveis.
- Evitar valores hardcoded — preferir variáveis CSS / design tokens.
- `ViewEncapsulation.Emulated` (padrão) — usar `None` apenas quando justificado.

### Estrutura de Arquivos
- Seguir a estrutura já existente no projeto.
- Co-localizar testes (`.spec.ts`), modelos (`.models.ts`) e estilos junto ao componente.
- Feature modules / standalone routes com lazy loading.
- Barrel exports (`index.ts`) para módulos compartilhados.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guilherme-xmatch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
