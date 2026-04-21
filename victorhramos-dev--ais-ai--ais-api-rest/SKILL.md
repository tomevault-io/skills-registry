---
name: ais-api-rest
description: API REST e WebSocket para o sistema AIS. Use quando criar endpoints, Resources, validação ou eventos WebSocket. Use when this capability is needed.
metadata:
  author: victorhramos-dev
---

# AIS REST API & WebSocket Events

API REST com Laravel + WebSocket via Reverb para eventos em tempo real.

## Endpoints Disponíveis

### Messages

```
GET    /api/v1/ais/messages           Lista mensagens (paginado)
GET    /api/v1/ais/messages/{id}      Detalhes de mensagem
GET    /api/v1/ais/messages/latest    Últimas mensagens (streaming-ready)
```

### Vessels

```
GET    /api/v1/ais/vessels            Lista navios conhecidos
GET    /api/v1/ais/vessels/{mmsi}     Detalhes de navio por MMSI
GET    /api/v1/ais/vessels/{mmsi}/track   Histórico de posições
```

### Analysis

```
GET    /api/v1/ais/analysis           Lista análises LLM
GET    /api/v1/ais/analysis/{id}      Detalhes de análise
POST   /api/v1/ais/analysis/trigger   Dispara análise manual
```

### Antennas

```
GET    /api/v1/ais/antennas           Lista antenas configuradas
GET    /api/v1/ais/antennas/{id}      Detalhes de antena
PUT    /api/v1/ais/antennas/{id}      Atualiza configuração
```

## Controller Padrão

```php
<?php

namespace App\Http\Controllers\API\AIS;

use App\Http\Controllers\Controller;
use App\Http\Resources\AIS\MessageResource;
use App\Models\AISMessage;
use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\AnonymousResourceCollection;

class MessageController extends Controller
{
    /**
     * Lista mensagens AIS com filtros e paginação.
     *
     * @param Request $request
     * @return AnonymousResourceCollection
     */
    public function index(Request $request): AnonymousResourceCollection
    {
        $validated = $request->validate([
            'mmsi' => 'sometimes|string|size:9',
            'type' => 'sometimes|integer|between:1,27',
            'from' => 'sometimes|date',
            'to' => 'sometimes|date|after:from',
            'limit' => 'sometimes|integer|between:1,100',
        ]);

        $query = AISMessage::query();

        // Aplicar filtros
        if (isset($validated['mmsi'])) {
            $query->where('mmsi', $validated['mmsi']);
        }

        if (isset($validated['type'])) {
            $query->where('message_type', $validated['type']);
        }

        if (isset($validated['from'])) {
            $query->where('received_at', '>=', $validated['from']);
        }

        if (isset($validated['to'])) {
            $query->where('received_at', '<=', $validated['to']);
        }

        $limit = $validated['limit'] ?? 50;

        return MessageResource::collection(
            $query->orderBy('received_at', 'desc')
                  ->paginate($limit)
        );
    }

    /**
     * Exibe detalhes de mensagem específica.
     *
     * @param string $id
     * @return MessageResource
     */
    public function show(string $id): MessageResource
    {
        $message = AISMessage::findOrFail($id);
        return new MessageResource($message);
    }
}
```

## Resource (DTO)

```php
<?php

namespace App\Http\Resources\AIS;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class MessageResource extends JsonResource
{
    /**
     * Transforma recurso em array.
     *
     * @param Request $request
     * @return array<string, mixed>
     */
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'mmsi' => $this->mmsi,
            'type' => $this->message_type,
            'position' => [
                'latitude' => $this->latitude,
                'longitude' => $this->longitude,
            ],
            'navigation' => [
                'speed' => $this->speed_over_ground,
                'course' => $this->course_over_ground,
                'heading' => $this->true_heading,
                'status' => $this->navigation_status,
            ],
            'received_at' => $this->received_at->toISOString(),
            'vessel' => new VesselResource($this->whenLoaded('vessel')),
        ];
    }
}
```

## WebSocket Events

### MessageReceived

```php
<?php

namespace App\Events\AIS;

use Illuminate\Broadcasting\Channel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

class MessageReceived implements ShouldBroadcast
{
    public function __construct(
        public array $message
    ) {}

    public function broadcastOn(): Channel
    {
        return new Channel('ais.messages');
    }

    public function broadcastAs(): string
    {
        return 'message.received';
    }

    public function broadcastWith(): array
    {
        return [
            'mmsi' => $this->message['mmsi'],
            'type' => $this->message['type'],
            'position' => [
                'lat' => $this->message['latitude'],
                'lon' => $this->message['longitude'],
            ],
            'timestamp' => now()->toISOString(),
        ];
    }
}
```

### AnalysisCompleted

```php
class AnalysisCompleted implements ShouldBroadcast
{
    public function __construct(
        public AnalysisSummary $analysis
    ) {}

    public function broadcastOn(): Channel
    {
        return new Channel('ais.analysis');
    }

    public function broadcastAs(): string
    {
        return 'analysis.completed';
    }
}
```

## Autenticação

```php
// Sanctum para API tokens
Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('messages', MessageController::class)->only(['index', 'show']);
    Route::apiResource('vessels', VesselController::class)->only(['index', 'show']);
    Route::apiResource('analysis', AnalysisController::class);
});
```

## Rate Limiting

```php
// app/Providers/RouteServiceProvider.php
RateLimiter::for('ais-api', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});
```

## Rotas

```php
// routes/api.php
Route::prefix('v1/ais')->group(function () {
    Route::apiResource('messages', MessageController::class)->only(['index', 'show']);
    Route::get('messages/latest', [MessageController::class, 'latest']);
    
    Route::apiResource('vessels', VesselController::class)->only(['index', 'show']);
    Route::get('vessels/{mmsi}/track', [VesselController::class, 'track']);
    
    Route::apiResource('analysis', AnalysisController::class);
    Route::post('analysis/trigger', [AnalysisController::class, 'trigger']);
    
    Route::apiResource('antennas', AntennaController::class)->except(['store', 'destroy']);
});
```

## Regras

- SEMPRE validar input com `$request->validate()`
- SEMPRE usar Resources para output (nunca retornar Models direto)
- SEMPRE paginar listagens (máximo 100 itens)
- Rate limiting obrigatório (60 req/min padrão)
- Autenticação via Sanctum para endpoints sensíveis
- WebSocket para eventos em tempo real (não polling)

## Referências

- @.docs/SPEC-06: API Endpoints & WebSocket Events.md para detalhes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/victorhramos-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
