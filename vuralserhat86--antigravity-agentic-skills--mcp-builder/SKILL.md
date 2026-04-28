---
name: mcp-builder
description: MCP (Model Context Protocol) server oluşturma, FastMCP/TypeScript SDK kullanımı ve API entegrasyonu rehberi. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 🔌 MCP Builder

> MCP server oluşturma ve API entegrasyonu rehberi.

---

## 📋 MCP Nedir?

Model Context Protocol (MCP), LLM'lerin dış servislerle etkileşim kurmasını sağlayan standart bir protokoldür.

### Kullanım Alanları
- API entegrasyonları
- Veritabanı bağlantıları
- Dosya sistemi erişimi
- Harici servis çağrıları

---

## 🐍 Python (FastMCP)

### Kurulum
```bash
pip install fastmcp
```

### Basit Server
```python
from fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.tool()
def hello(name: str) -> str:
    """Say hello to someone."""
    return f"Hello, {name}!"

@mcp.tool()
def add(a: int, b: int) -> int:
    """Add two numbers."""
    return a + b

if __name__ == "__main__":
    mcp.run()
```

### Resource Ekleme
```python
@mcp.resource("config://app")
def get_config() -> str:
    """Get application configuration."""
    return json.dumps({"version": "1.0"})
```

---

## 📘 TypeScript (MCP SDK)

### Kurulum
```bash
npm install @modelcontextprotocol/sdk
```

### Server Oluşturma
```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new McpServer({
  name: "my-server",
  version: "1.0.0",
});

server.tool("hello", { name: "string" }, async ({ name }) => {
  return { content: [{ type: "text", text: `Hello, ${name}!` }] };
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

---

## ⚙️ Yapılandırma

### mcp_config.json
```json
{
  "mcpServers": {
    "my-server": {
      "command": "python",
      "args": ["path/to/server.py"],
      "env": {
        "API_KEY": "your-key"
      }
    }
  }
}
```

---

## 🎯 Best Practices

1. **Tool naming**: Açıklayıcı, lowercase, hyphen-separated
2. **Descriptions**: Her tool için detaylı docstring
3. **Error handling**: Try-catch ile hata yönetimi
4. **Type hints**: Parametre tipleri belirt
5. **Validation**: Input validation yap

---

*MCP Builder v1.1 - Enhanced*

## 🔄 Workflow

> **Kaynak:** [Model Context Protocol Spec](https://spec.modelcontextprotocol.io/)

### Aşama 1: Tool Definition
- [ ] **Schema**: Input şemasını JSON Schema standardına uygun (ve Zod ile valide edilebilir) tanımla.
- [ ] **Description**: LLM'in ne zaman kullanacağını anlaması için "açıklayıcı" ve "örnekli" docstring yaz.
- [ ] **Idempotency**: Tool'un tekrar çağrıldığında yan etkisi olup olmadığını belirt.

### Aşama 2: Implementation & Security
- [ ] **Isolation**: Dosya sistemi erişimini sadece izin verilen dizinlerle sınırla.
- [ ] **Validation**: Kullanıcı girdilerini her zaman sanitize et (Path Traversal vb. önle).
- [ ] **Transport**: Stdio veya SSE transport katmanını doğru yapılandır.

### Aşama 3: Testing
- [ ] **Inspector**: MCP Inspector aracı ile endpointleri manuel test et.
- [ ] **Integration**: Claude Desktop (veya hedef client) ile uçtan uca test yap.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | LLM tool'u doğru parametrelerle çağırabiliyor mu? |
| 2 | Hata durumunda (örn: dosya yok) anlamlı bir mesaj dönüyor mu? |
| 3 | Server başlatıldığında resource tüketimi (RAM/CPU) makul mü? |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
