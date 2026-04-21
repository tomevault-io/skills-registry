---
name: ais-deduplication
description: Sistema de deduplicação 3-camadas para mensagens AIS. Use quando trabalhar com Redis, Bloom Filter, ou lógica de deduplicação. Use when this capability is needed.
metadata:
  author: victorhramos-dev
---

# AIS Deduplication Service (3-Layer Strategy)

Deduplicação em 3 camadas para alto throughput com garantia de unicidade.

## Arquitetura 3-Camadas

```
Mensagem AIS
     │
     ▼
┌─────────────────────────────────────┐
│ Camada 1: Redis SET NX              │  ◄── Rápido (< 1ms)
│ TTL: 60 segundos                    │      Volátil
└─────────────────────────────────────┘
     │ (não existe)
     ▼
┌─────────────────────────────────────┐
│ Camada 2: Bloom Filter              │  ◄── Muito rápido (< 0.1ms)
│ Probabilístico (FP: 0.01%)          │      Memória eficiente
└─────────────────────────────────────┘
     │ (provavelmente não existe)
     ▼
┌─────────────────────────────────────┐
│ Camada 3: PostgreSQL UPSERT         │  ◄── Definitivo
│ ON CONFLICT DO NOTHING              │      Persistente
└─────────────────────────────────────┘
     │
     ▼
  Mensagem Única
```

## Geração de Hash

```php
/**
 * Gera hash de deduplicação para mensagem AIS.
 * 
 * Combina: MMSI + tipo + timestamp (janela 30s) + payload
 * 
 * @param array $mensagem Dados da mensagem AIS
 * @return string Hash SHA256 de 64 caracteres
 */
public function gerarHash(array $mensagem): string
{
    // Janela temporal de 30 segundos para agrupar mensagens similares
    $janelaTempooral = floor($mensagem['timestamp'] / 30) * 30;
    
    $dados = implode('|', [
        $mensagem['mmsi'],
        $mensagem['message_type'],
        $janelaTempooral,
        $mensagem['raw_payload'],
    ]);
    
    return hash('sha256', $dados);
}
```

## Camada 1: Redis SET NX

```php
private const REDIS_PREFIX = 'ais:dedup:';
private const REDIS_TTL = 60; // segundos

/**
 * Verifica duplicação via Redis (camada 1).
 * 
 * @param string $hash Hash da mensagem
 * @return bool true se duplicada, false se nova
 */
private function verificarRedis(string $hash): bool
{
    // SET NX retorna false se já existe
    $resultado = Redis::set(
        self::REDIS_PREFIX . $hash,
        '1',
        'EX', self::REDIS_TTL,
        'NX'
    );
    
    return $resultado === false;
}
```

## Camada 2: Bloom Filter (ReBloom)

```php
private const BLOOM_KEY = 'ais:bloom:messages';
private const BLOOM_ERROR_RATE = 0.0001; // 0.01%
private const BLOOM_CAPACITY = 10_000_000; // 10M mensagens

/**
 * Verifica duplicação via Bloom Filter (camada 2).
 * 
 * @param string $hash Hash da mensagem
 * @return bool true se provavelmente duplicada
 */
private function verificarBloom(string $hash): bool
{
    // BF.EXISTS retorna 1 se provavelmente existe
    $existe = Redis::executeRaw(['BF.EXISTS', self::BLOOM_KEY, $hash]);
    
    if (!$existe) {
        // Adicionar ao filtro
        Redis::executeRaw(['BF.ADD', self::BLOOM_KEY, $hash]);
    }
    
    return $existe === 1;
}

/**
 * Inicializa Bloom Filter com capacidade e taxa de erro.
 */
public function initializeBloomFilter(): void
{
    Redis::executeRaw([
        'BF.RESERVE',
        self::BLOOM_KEY,
        self::BLOOM_ERROR_RATE,
        self::BLOOM_CAPACITY,
    ]);
}
```

## Camada 3: PostgreSQL UPSERT

```php
/**
 * Insere mensagem com deduplicação final via PostgreSQL.
 * 
 * @param array $mensagem Dados completos da mensagem
 * @return bool true se inserida, false se duplicada
 */
private function inserirComDedup(array $mensagem): bool
{
    $resultado = DB::connection('ais')->insert('
        INSERT INTO ais_messages (
            mmsi, message_type, raw_nmea, parsed_data, 
            dedup_hash, received_at
        ) VALUES (?, ?, ?, ?, ?, ?)
        ON CONFLICT (dedup_hash, received_at) DO NOTHING
    ', [
        $mensagem['mmsi'],
        $mensagem['type'],
        $mensagem['raw'],
        json_encode($mensagem['parsed']),
        $mensagem['hash'],
        $mensagem['received_at'],
    ]);
    
    return $resultado > 0;
}
```

## Fluxo Completo

```php
/**
 * Processa mensagem AIS com deduplicação 3-camadas.
 * 
 * @param array $mensagem Dados da mensagem
 * @return bool true se processada (nova), false se duplicada
 */
public function processar(array $mensagem): bool
{
    $hash = $this->gerarHash($mensagem);
    
    // Camada 1: Redis (rápido, volátil)
    if ($this->verificarRedis($hash)) {
        $this->metricas->incrementar('dedup.redis.hits');
        return false;
    }
    
    // Camada 2: Bloom Filter (probabilístico)
    if ($this->verificarBloom($hash)) {
        $this->metricas->incrementar('dedup.bloom.hits');
        // Possível falso positivo, continuar para camada 3
    }
    
    // Camada 3: PostgreSQL (definitivo)
    $mensagem['hash'] = $hash;
    $inserido = $this->inserirComDedup($mensagem);
    
    if (!$inserido) {
        $this->metricas->incrementar('dedup.db.hits');
    }
    
    return $inserido;
}
```

## Métricas

```php
// Monitorar taxa de duplicação por camada
$metricas = [
    'dedup.redis.hits' => 0,    // Rejeitadas na camada 1
    'dedup.bloom.hits' => 0,    // Suspeitas na camada 2
    'dedup.db.hits' => 0,       // Rejeitadas na camada 3
    'dedup.total.processed' => 0,
    'dedup.total.unique' => 0,
];
```

## Regras

- SEMPRE verificar camadas em ordem (1 → 2 → 3)
- TTL Redis DEVE ser curto (60s) para não desperdiçar memória
- Bloom Filter DEVE ser reinicializado em deploy
- Taxa de falso positivo do Bloom < 0.1%
- Janela temporal para hash: 30 segundos

## Referências

- @.docs/SPEC-02: Deduplication Service.md para detalhes completos

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/victorhramos-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
