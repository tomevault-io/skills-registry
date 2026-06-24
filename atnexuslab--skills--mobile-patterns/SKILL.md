---
name: mobile-patterns
description: Use quando precisar implementar features mobile (React Native, Flutter, Swift, Kotlin), configurar builds, resolver problemas de plataforma ou adaptar UI para mobile. Use when this capability is needed.
metadata:
  author: ATNexusLab
---

# Mobile Patterns

> ⚠️ EXPERIMENTAL — Ainda não validado em produção.

## Quando Usar

- Implementar features em React Native, Flutter, Swift ou Kotlin
- Configurar builds para iOS e Android
- Resolver problemas específicos de plataforma
- Adaptar componentes web para comportamento mobile

## Padrões Fundamentais

### Performance Mobile

- Evitar re-renders em listas longas: usar `FlatList` (RN) ou `ListView.builder` (Flutter) — nunca renderizar tudo de uma vez
- Imagens: usar CDN, tamanhos otimizados, lazy loading
- Animações: rodar na thread nativa, não na JS thread (React Native: `useNativeDriver: true`)
- Evitar operações síncronas pesadas na main thread

### Navegação

- Separar navegação de lógica de negócio
- Gerenciar deep links com atenção a segurança (validar parâmetros)
- Preservar estado de scroll e posição ao voltar de screens

### Offline e Conectividade

- Detectar estado de rede antes de operações críticas
- Implementar retry com backoff para chamadas de API
- Cache local para dados frequentemente acessados
- Sincronização de dados offline com conflito handling

### Segurança Mobile

- Nunca armazenar secrets ou tokens no código
- Usar Keychain (iOS) ou Keystore (Android) para dados sensíveis
- Certificate pinning para APIs críticas
- Ofuscação de código em builds de produção

### Build e Deploy

```bash
# React Native — build Android
cd android && ./gradlew assembleRelease

# React Native — build iOS
xcodebuild -workspace ios/App.xcworkspace -scheme App -configuration Release

# Flutter — build
flutter build apk --release
flutter build ios --release
```

## Checklist Pré-Release

- [ ] Testado em dispositivo real (não só emulador)
- [ ] Testado nos tamanhos de tela principais
- [ ] Permissões solicitadas com justificativa clara
- [ ] Deep links testados
- [ ] Performance em dispositivos de baixo desempenho
- [ ] Sem dados sensíveis em logs de produção
- [ ] Build assinado com certificado correto

## Passos

### 1. Identificar a plataforma alvo

- **React Native**: JavaScript/TypeScript, código compartilhado iOS+Android
- **Flutter**: Dart, alta fidelidade visual multiplataforma
- **Swift/SwiftUI**: iOS/macOS nativo
- **Kotlin/Jetpack Compose**: Android nativo

### 2. Mapear os padrões fundamentais necessários

Consultar `## Padrões Fundamentais` para identificar quais aplicam:
- Navegação (Stack, Tab, Drawer)
- Gerenciamento de estado
- Requisições HTTP e cache
- Armazenamento local

### 3. Implementar com padrões da plataforma

- Seguir as **Human Interface Guidelines** (iOS) ou **Material Design** (Android)
- Componentes nativos sempre que possível (evitar custom widgets desnecessários)
- Adaptar layout para diferentes tamanhos de tela (`SafeAreaView`, responsive units)

### 4. Validar em dispositivo real

- Testar em iOS e Android (não só em emulador)
- Verificar orientação portrait e landscape
- Testar com conectividade ruim e offline

### 5. Completar o checklist antes de release

Usar `## Checklist Pré-Release` antes de submeter para as stores.

## Exemplos

### React Native — Componente com estados de loading/error

```tsx
function ListaPedidos() {
  const { data, isLoading, error } = usePedidos()

  if (isLoading) return <ActivityIndicator size="large" />
  if (error) return <ErrorState message={error.message} onRetry={refetch} />
  if (!data?.length) return <EmptyState message="Nenhum pedido encontrado" />

  return (
    <FlatList
      data={data}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => <PedidoCard pedido={item} />}
    />
  )
}
```

### Flutter — Tratamento de estado com riverpod

```dart
final pedidosProvider = FutureProvider<List<Pedido>>((ref) async {
  return ref.watch(pedidoRepositoryProvider).listarPedidos();
});

class PedidosScreen extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final pedidosAsync = ref.watch(pedidosProvider);
    return pedidosAsync.when(
      loading: () => const CircularProgressIndicator(),
      error: (err, _) => Text('Erro: $err'),
      data: (pedidos) => ListView.builder(
        itemCount: pedidos.length,
        itemBuilder: (ctx, i) => PedidoTile(pedido: pedidos[i]),
      ),
    );
  }
}
```

---
> Source: [ATNexusLab/skills](https://github.com/ATNexusLab/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
