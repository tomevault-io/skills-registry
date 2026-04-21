---
name: ais-analysis-llm
description: Sistema de análise de tráfego marítimo com LLM (Minimax.io). Use quando trabalhar com AnalysisService, prompts LLM, triggers de análise ou integração com Minimax. Use when this capability is needed.
metadata:
  author: victorhramos-dev
---

# AIS Analysis Engine & LLM Integration

Análise inteligente de tráfego marítimo com Minimax.io API.

## Arquitetura

```
┌─────────────────────────────────────────────────────────────────┐
│                    Analysis Trigger Service                      │
│                                                                  │
│   Intervalo: 3 minutos   OU   Batch: 100 mensagens              │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Analysis Service                            │
│                                                                  │
│   1. Coletar mensagens do período                               │
│   2. Agregar por navio (MMSI)                                   │
│   3. Construir contexto                                         │
│   4. Chamar LLM                                                 │
│   5. Persistir resultado                                        │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Minimax Service                             │
│                                                                  │
│   - Retry com backoff exponencial                               │
│   - Timeout: 30 segundos                                        │
│   - Modelo: abab6.5-chat                                        │
└─────────────────────────────────────────────────────────────────┘
```

## MinimaxService

```php
<?php

namespace App\Services\AI;

use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Log;

class MinimaxService implements MinimaxServiceInterface
{
    private const API_URL = 'https://api.minimax.chat/v1/text/chatcompletion_v2';
    private const MODEL = 'abab6.5-chat';
    private const TIMEOUT = 30;
    private const MAX_RETRIES = 3;

    public function __construct(
        protected string $apiKey,
        protected string $groupId
    ) {}

    /**
     * Envia prompt para análise e retorna resposta.
     *
     * @param string $systemPrompt Prompt de sistema
     * @param string $userPrompt Prompt do usuário com dados
     * @return array{content: string, tokens: int, latency_ms: int}
     * @throws MinimaxException Se falhar após retries
     */
    public function analisar(string $systemPrompt, string $userPrompt): array
    {
        $tentativas = 0;
        $ultimoErro = null;

        while ($tentativas < self::MAX_RETRIES) {
            try {
                $inicio = microtime(true);
                
                $response = Http::timeout(self::TIMEOUT)
                    ->withHeaders([
                        'Authorization' => "Bearer {$this->apiKey}",
                        'Content-Type' => 'application/json',
                    ])
                    ->post(self::API_URL, [
                        'model' => self::MODEL,
                        'messages' => [
                            ['role' => 'system', 'content' => $systemPrompt],
                            ['role' => 'user', 'content' => $userPrompt],
                        ],
                        'temperature' => 0.7,
                        'max_tokens' => 2000,
                    ]);

                $latencia = (int)((microtime(true) - $inicio) * 1000);

                if ($response->successful()) {
                    $data = $response->json();
                    return [
                        'content' => $data['choices'][0]['message']['content'],
                        'tokens' => $data['usage']['total_tokens'],
                        'latency_ms' => $latencia,
                    ];
                }

                throw new MinimaxException("API error: " . $response->status());

            } catch (\Exception $e) {
                $ultimoErro = $e;
                $tentativas++;
                
                // Backoff exponencial: 1s, 2s, 4s
                if ($tentativas < self::MAX_RETRIES) {
                    sleep(pow(2, $tentativas - 1));
                }
            }
        }

        Log::error('Minimax falhou após retries', [
            'tentativas' => $tentativas,
            'erro' => $ultimoErro->getMessage(),
        ]);

        throw new MinimaxException(
            "Falha após {$tentativas} tentativas: " . $ultimoErro->getMessage()
        );
    }
}
```

## System Prompt (Porto de Santos)

```php
private const SYSTEM_PROMPT = <<<'PROMPT'
Você é um analista especializado em tráfego marítimo do Porto de Santos, 
o maior porto da América Latina. Sua função é analisar dados AIS (Automatic 
Identification System) e gerar insights sobre o movimento de embarcações.

## Contexto Geográfico
- Porto de Santos: -23.9850 a -24.0200 latitude
- Canal de navegação principal
- Áreas de fundeio designadas
- Terminais de contêineres, granéis e petroleiros

## Sua Análise Deve Incluir
1. **Resumo do Período**: Volume de tráfego, navios ativos
2. **Movimentações Relevantes**: Chegadas, partidas, manobras
3. **Alertas**: Situações incomuns, riscos potenciais
4. **Padrões**: Tendências observadas no período

## Formato de Resposta
Responda em português brasileiro, de forma técnica mas acessível.
Seja conciso e objetivo. Destaque informações críticas.

## Níveis de Risco
- BAIXO: Operação normal
- MÉDIO: Atenção recomendada
- ALTO: Ação imediata necessária
- CRÍTICO: Emergência
PROMPT;
```

## AnalysisTriggerService

```php
<?php

namespace App\Services\AIS;

class AnalysisTriggerService
{
    private const INTERVALO_MINUTOS = 3;
    private const BATCH_SIZE = 100;

    private int $mensagensDesdeUltimaAnalise = 0;
    private \DateTime $ultimaAnalise;

    public function __construct(
        protected AnalysisService $analysisService
    ) {
        $this->ultimaAnalise = new \DateTime();
    }

    /**
     * Chamado a cada mensagem processada.
     * Verifica se deve disparar análise.
     */
    public function onMessage(): void
    {
        $this->mensagensDesdeUltimaAnalise++;

        // Trigger por batch
        if ($this->mensagensDesdeUltimaAnalise >= self::BATCH_SIZE) {
            $this->dispararAnalise('batch');
            return;
        }

        // Trigger por intervalo
        $agora = new \DateTime();
        $diff = $agora->diff($this->ultimaAnalise);
        $minutos = ($diff->h * 60) + $diff->i;

        if ($minutos >= self::INTERVALO_MINUTOS) {
            $this->dispararAnalise('interval');
        }
    }

    /**
     * Dispara job de análise assíncrono.
     */
    private function dispararAnalise(string $triggerType): void
    {
        RunAnalysis::dispatch($triggerType);
        
        $this->mensagensDesdeUltimaAnalise = 0;
        $this->ultimaAnalise = new \DateTime();
    }

    /**
     * Força análise manual.
     */
    public function forcarGatilho(): void
    {
        $this->dispararAnalise('manual');
    }
}
```

## AnalysisService

```php
<?php

namespace App\Services\AIS;

class AnalysisService
{
    public function __construct(
        protected MinimaxService $minimax,
        protected AISMessage $messageModel,
        protected AnalysisSummary $summaryModel
    ) {}

    /**
     * Executa análise do período.
     *
     * @param string $triggerType Tipo do gatilho (interval|batch|manual)
     * @return AnalysisSummary
     */
    public function executar(string $triggerType): AnalysisSummary
    {
        // 1. Coletar mensagens desde última análise
        $ultimaAnalise = $this->summaryModel
            ->latest('generated_at')
            ->first();
        
        $desde = $ultimaAnalise?->generated_at ?? now()->subMinutes(5);
        
        $mensagens = $this->messageModel
            ->where('received_at', '>=', $desde)
            ->orderBy('received_at')
            ->get();

        // 2. Agregar por navio
        $porNavio = $mensagens->groupBy('mmsi');

        // 3. Construir contexto para LLM
        $contexto = $this->construirContexto($porNavio, $desde, now());

        // 4. Chamar LLM
        $resultado = $this->minimax->analisar(
            self::SYSTEM_PROMPT,
            $contexto
        );

        // 5. Persistir resultado
        return $this->summaryModel->create([
            'trigger_type' => $triggerType,
            'period_start' => $desde,
            'period_end' => now(),
            'messages_analyzed' => $mensagens->count(),
            'vessels_unique' => $porNavio->count(),
            'summary_text' => $resultado['content'],
            'llm_model' => 'abab6.5-chat',
            'tokens_used' => $resultado['tokens'],
            'latency_ms' => $resultado['latency_ms'],
            'generated_at' => now(),
        ]);
    }
}
```

## RunAnalysis Job

```php
<?php

namespace App\Jobs\AIS;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class RunAnalysis implements ShouldQueue
{
    use Queueable, InteractsWithQueue;

    public $queue = 'ais-analysis';
    public $tries = 3;
    public $backoff = [60, 120, 300];

    public function __construct(
        public string $triggerType
    ) {}

    public function handle(AnalysisService $service): void
    {
        $summary = $service->executar($this->triggerType);
        
        // Broadcast resultado via WebSocket
        event(new AnalysisCompleted($summary));
    }
}
```

## Regras

- SEMPRE usar retry com backoff exponencial para API externa
- NUNCA bloquear receiver esperando análise (usar job assíncrono)
- Timeout de 30s para chamadas LLM
- Prompt em português para contexto Porto de Santos
- Persistir métricas (tokens, latência) para monitoramento
- Rate limit: máximo 20 análises/hora

## Referências

- @.docs/SPEC-05: Analysis Engine & LLM Integration.md para detalhes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/victorhramos-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
