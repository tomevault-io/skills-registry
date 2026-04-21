---
name: ais-parser-python
description: Parser AIS PHP-Python bridge usando pyais. Use quando trabalhar com decodificação de mensagens NMEA/AIS, comunicação entre PHP e Python, ou modificar o parser. Use when this capability is needed.
metadata:
  author: victorhramos-dev
---

# AIS Parser Service (PHP-Python Bridge)

Decodificação de mensagens AIS via pyais com comunicação JSON over STDIN/STDOUT.

## Arquitetura

```
PHP (Laravel)                    Python (pyais)
     │                                │
     │  JSON Request (STDIN)          │
     ├───────────────────────────────▶│
     │                                │
     │  JSON Response (STDOUT)        │
     │◀───────────────────────────────┤
     │                                │
```

## Script Python (python/ais_parser.py)

```python
#!/usr/bin/env python3
"""
Parser AIS usando pyais.
Recebe sentenças NMEA via STDIN, retorna JSON via STDOUT.
"""

import sys
import json
from pyais import decode

def parse_nmea(sentence: str) -> dict:
    """
    Decodifica sentença NMEA AIS.
    
    Args:
        sentence: Sentença NMEA completa (ex: !AIVDM,1,1,,A,13HOI:0P0...)
        
    Returns:
        Dict com campos decodificados ou erro
    """
    try:
        decoded = decode(sentence)
        return {
            "success": True,
            "data": decoded.asdict()
        }
    except Exception as e:
        return {
            "success": False,
            "error": str(e)
        }

def main():
    """Loop principal: lê STDIN, processa, escreve STDOUT."""
    for line in sys.stdin:
        line = line.strip()
        if not line:
            continue
            
        try:
            request = json.loads(line)
            sentence = request.get("sentence", "")
            result = parse_nmea(sentence)
        except json.JSONDecodeError as e:
            result = {"success": False, "error": f"JSON inválido: {e}"}
        
        print(json.dumps(result), flush=True)

if __name__ == "__main__":
    main()
```

## Service PHP (app/Services/AIS/ParserService.php)

```php
<?php

namespace App\Services\AIS;

use Symfony\Component\Process\Process;

class ParserService implements ParserServiceInterface
{
    private const PYTHON_SCRIPT = 'python/ais_parser.py';
    private const TIMEOUT = 5;

    private ?Process $process = null;

    /**
     * Decodifica sentença NMEA AIS.
     *
     * @param string $sentence Sentença NMEA completa
     * @return array<string, mixed> Dados decodificados
     * @throws ParserException Se falhar
     */
    public function parse(string $sentence): array
    {
        $this->ensureProcessRunning();
        
        // Enviar request JSON
        $request = json_encode(['sentence' => $sentence]) . "\n";
        $this->process->getInput()->write($request);
        
        // Ler response
        $response = fgets($this->process->getOutput());
        $result = json_decode($response, true);
        
        if (!$result['success']) {
            throw new ParserException($result['error']);
        }
        
        return $result['data'];
    }

    /**
     * Garante que o processo Python está rodando.
     */
    private function ensureProcessRunning(): void
    {
        if ($this->process === null || !$this->process->isRunning()) {
            $this->process = new Process([
                'python3',
                base_path(self::PYTHON_SCRIPT)
            ]);
            $this->process->setTimeout(null);
            $this->process->start();
        }
    }
}
```

## Formato NMEA AIS

```
!AIVDM,1,1,,A,13HOI:0P0000VOHLCnHQKwvL05Ip,0*44
│     │ │  │ │ │                              │
│     │ │  │ │ │                              └── Checksum
│     │ │  │ │ └── Payload (armor-encoded)
│     │ │  │ └─── Channel (A/B)
│     │ │  └──── Fragment number
│     │ └─────── Total fragments
│     └───────── Message type
└────────────── Start delimiter
```

## Tipos de Mensagem AIS

| Tipo | Nome | Campos Principais |
|------|------|-------------------|
| 1-3 | Position Report Class A | mmsi, lat, lon, speed, course, heading |
| 5 | Static and Voyage Data | name, imo, callsign, destination, eta |
| 18 | Position Report Class B | mmsi, lat, lon, speed, course |
| 24 | Class B Static Data | name, shiptype, callsign |

## Pool de Processos

Para alta performance, manter pool de processos Python:

```php
class ParserPool
{
    private const POOL_SIZE = 4;
    private array $processes = [];
    private int $currentIndex = 0;

    public function parse(string $sentence): array
    {
        // Round-robin entre processos
        $process = $this->processes[$this->currentIndex];
        $this->currentIndex = ($this->currentIndex + 1) % self::POOL_SIZE;
        
        return $this->sendToProcess($process, $sentence);
    }
}
```

## Regras

- SEMPRE validar checksum antes de parsear
- NUNCA bloquear esperando resposta indefinidamente (timeout 5s)
- Pool de processos para throughput > 100 msg/s
- Logs de erros em português para debugging

## Referências

- @.docs/SPEC-03: Parser Service (PHP-Python Bridge).md para detalhes
- https://gpsd.gitlab.io/gpsd/AIVDM.html para documentação AIS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/victorhramos-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
