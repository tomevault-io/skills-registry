---
name: skill-android-loto-orchestrator
description: Orquestra desenvolvimento Android para o Loto Generator com Clean Architecture, domínio em Kotlin puro, dados offline-first (Room + WorkManager + Retrofit), UI em Jetpack Compose Material 3 e guardrails de performance. Use quando precisar projetar, revisar ou implementar features end-to-end envolvendo contratos entre camadas, sync resiliente, regras de negócio, estado de tela e otimizações de execução. Use when this capability is needed.
metadata:
  author: pliniou
---

# Skill Android Loto Orchestrator

Alinhar decisões entre arquitetura, domínio, dados, UI e performance para manter o app escalável, testável e rápido.

## Definir Regras Inegociáveis

- Manter `domain` puro Kotlin, sem dependência de Android/framework.
- Fazer dependências fluírem para dentro: `ui -> domain <- data(impl)`.
- Centralizar regras de negócio em use cases/entidades do domínio.
- Guiar UI por dados locais (`offline-first`) e atualizar em background.
- Não bloquear main thread com I/O, parsing, sync ou cálculos pesados.

## Planejar Feature End-to-End

Para cada feature, produzir este recorte mínimo antes de codar:

1. Caso de uso e regras de negócio (domain).
2. Contrato de repositório (interface no domain).
3. Estratégia de persistência/sync (data).
4. Estado e eventos de tela (ui/viewmodel).
5. Riscos de performance e testes de regressão.

## Arquitetar Camadas

- Definir pastas/módulos por `ui`, `domain`, `data` (ou por feature mantendo essas fronteiras).
- Criar interfaces de repositório no domínio; implementar no data.
- Proibir lógica de negócio em composables, activities/fragments e DAOs.
- Expor modelos de resultado explícitos (`Result` ou sealed classes) para sucesso/erro.

## Aplicar Injeção de Dependência (Hilt)

- Usar `@HiltAndroidApp` na aplicação.
- Fornecer implementações via módulos Hilt por camada.
- Usar escopos corretos no grafo:
- `@Singleton` para DB, API, repositórios singleton.
- `@ViewModelScoped`/`@ActivityRetainedScoped` para dependências de estado de tela.
- Validar grafo DI para garantir que UI não acesse detalhes de infra diretamente.

## Implementar Offline-First

- Ler sempre do Room primeiro e observar por `Flow`.
- Executar sync silencioso com WorkManager quando houver conectividade.
- Aplicar stale-while-revalidate: cache imediato, refresh em background.
- Mapear `DTO -> entidade/data -> modelo de domínio` antes de expor dados.
- Tratar migração legada (`lottery_data.json`) no startup para Room e remover fonte antiga.
- Versionar schema de assets e manter parser com fallback controlado.
- Registrar staleness por modalidade e sinalizar dado desatualizado sem quebrar fluxo.

## Implementar UI Compose

- Construir telas com Compose + Material 3, sem hardcode de cor/tipografia.
- Modelar `UiState` explícito: `Loading`, `Content`, `Empty`, `Error`.
- Aplicar unidirectional data flow: UI renderiza estado e dispara eventos.
- Fazer state hoisting; manter composables preferencialmente stateless.
- Tratar acessibilidade (content descriptions, touch target >= 48dp, contraste).
- Usar navegação com contratos claros de argumentos.

## Proteger Performance

- Definir metas: startup, frame time/jank, memória, bateria, rede.
- Evitar recomposição ampla: estados granulares, chaves estáveis em listas.
- Usar lazy layouts e paginação para grandes coleções.
- Garantir I/O e sync fora da main thread.
- Medir com profiling/macrobenchmark e corrigir gargalos antes de release.
- Manter release otimizado com R8 e recursos enxutos.

## Validar Qualidade

Antes de concluir qualquer feature, validar:

1. Regras de negócio cobertas por testes unitários de domínio.
2. Repositórios com cenários offline/online, erro e retry.
3. ViewModel com testes de estado/evento.
4. UI cobrindo estados principais e acessibilidade básica.
5. Sem quebra de fronteiras arquiteturais.

## Critérios de Conclusão

Considerar a entrega pronta somente quando:

- A feature funciona sem rede com dados locais existentes.
- Sync posterior atualiza dados e notifica UI via `Flow`.
- Não há lógica de negócio fora do domínio.
- Não há operação pesada na thread principal.
- Testes críticos passam e riscos remanescentes estão documentados.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pliniou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
