---
name: baml-integrator
description: Automatically integrate BAML into existing framework projects (React, FastAPI, Express, Django). Use when user wants to use BAML in their application. Use when this capability is needed.
metadata:
  author: orbruno
---

# BAML Framework Integrator

## When to Use This Skill

Automatically invoke this skill when:
- User mentions using BAML with a specific framework
- User asks how to call BAML functions from their app
- User wants to create API endpoints for BAML functions
- User mentions React hooks, FastAPI routes, Express endpoints
- User asks about deployment or production usage

## Examples That Trigger This Skill

- "How do I use this BAML function in my React app?"
- "Create a FastAPI endpoint for this BAML function"
- "Integrate this with my Next.js project"
- "I want to call this from my Express server"
- "Set up BAML with Django views"
- "Make this work with Streamlit"

## How to Use

1. **Detect framework**:
   - Identify from user's question or project files
   - Check package.json (Node/React), requirements.txt (Python), etc.
   - Determine language (Python, TypeScript, etc.)

2. **Verify BAML setup**:
   - Check for generated `baml_client/`
   - Identify which BAML functions exist
   - Verify dependencies are installed

3. **Framework-specific integration**:
   - Install required packages
   - Create appropriate integration code
   - Follow framework conventions
   - Include error handling

4. **Create working example**:
   - Full implementation (endpoint, hook, component)
   - Request/response types
   - Error handling patterns
   - Usage examples

5. **Environment setup**:
   - Configure API keys
   - Set up development workflow
   - Production deployment notes

## Framework Patterns

### React / Next.js

**Install dependency:**
```bash
npm install @boundaryml/baml-react
```

**Create hook:**
```typescript
// lib/baml.ts
import { useBAML } from '@boundaryml/baml-react';
import { b } from '../baml_client';

export function useExtractReceipt() {
  return useBAML(b.ExtractReceipt);
}
```

**Use in component:**
```typescript
'use client';

import { useExtractReceipt } from '@/lib/baml';

export default function ReceiptUploader() {
  const { execute, data, loading, error } = useExtractReceipt();

  const handleUpload = async (imageUrl: string) => {
    const result = await execute({ image: imageUrl });
    console.log(result.vendor, result.total);
  };

  return (
    <div>
      {loading && <p>Processing...</p>}
      {error && <p>Error: {error.message}</p>}
      {data && <div>Vendor: {data.vendor}, Total: ${data.total}</div>}
    </div>
  );
}
```

### FastAPI (Python)

**Create endpoint:**
```python
from fastapi import FastAPI, HTTPException
from baml_client import b
from pydantic import BaseModel

app = FastAPI()

class ReceiptRequest(BaseModel):
    image_url: str

@app.post("/api/extract-receipt")
async def extract_receipt(request: ReceiptRequest):
    try:
        result = await b.ExtractReceipt(image=request.image_url)
        return {
            "vendor": result.vendor,
            "total": result.total,
            "items": [{"name": i.name, "price": i.price} for i in result.items]
        }
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

### Express (TypeScript)

**Create route:**
```typescript
import express from 'express';
import { b } from './baml_client';

const app = express();
app.use(express.json());

app.post('/api/extract-receipt', async (req, res) => {
  try {
    const { imageUrl } = req.body;
    const result = await b.ExtractReceipt({ image: imageUrl });

    res.json({
      vendor: result.vendor,
      total: result.total,
      items: result.items
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

app.listen(3000);
```

### Django (Python)

**Create view:**
```python
from django.http import JsonResponse
from django.views import View
from baml_client import b
import json

class ExtractReceiptView(View):
    async def post(self, request):
        try:
            data = json.loads(request.body)
            result = await b.ExtractReceipt(image=data['image_url'])

            return JsonResponse({
                'vendor': result.vendor,
                'total': result.total,
                'items': [{'name': i.name, 'price': i.price} for i in result.items]
            })
        except Exception as e:
            return JsonResponse({'error': str(e)}, status=500)
```

## Integration Workflow

1. **Identify framework** from project structure
2. **Install dependencies** (baml-react, baml-py, etc.)
3. **Import BAML client** in appropriate files
4. **Create integration layer** (hooks, routes, views)
5. **Add error handling** and validation
6. **Provide usage examples** to user
7. **Configure environment variables** for API keys

## Deployment Considerations

### Environment Variables

Always configure:
```bash
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
# etc.
```

### Generated Code in CI/CD

**Option 1: Commit generated code** (simple but bulky)
```bash
git add baml_client/
git commit -m "Update BAML client"
```

**Option 2: Generate in build** (recommended)
```dockerfile
# In Dockerfile or build script
RUN baml generate
```

### Docker Example

```dockerfile
FROM python:3.11

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Install BAML CLI
RUN pip install baml-py

# Copy BAML source
COPY baml_src ./baml_src

# Generate client
RUN baml generate

# Copy app code
COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0"]
```

## Common Integration Patterns

### API Endpoint Pattern

For backend frameworks (FastAPI, Express, Django):
- Create POST endpoint
- Accept request with BAML function inputs
- Call BAML function with error handling
- Return structured response
- Include proper HTTP status codes

### React Hook Pattern

For React/Next.js:
- Use `useBAML` hook for state management
- Handle loading, error, data states
- Provide streaming support if needed
- Include TypeScript types

### Streaming Pattern

For long-running operations:
- Use Server-Sent Events (SSE) or WebSockets
- Stream partial results as they arrive
- Provide better user experience
- Handle disconnections gracefully

## Best Practices

1. **Error Handling**: Always wrap BAML calls in try/catch
2. **Type Safety**: Leverage generated types in TypeScript
3. **Environment Variables**: Never hardcode API keys
4. **Rate Limiting**: Implement on public endpoints
5. **Caching**: Cache identical requests when appropriate
6. **Monitoring**: Log BAML calls for debugging
7. **Validation**: Validate inputs before calling BAML
8. **Testing**: Write tests for integration layer

## Notes

- Each framework has idiomatic patterns - follow them
- BAML integrates seamlessly - no migration needed
- Generated clients are production-ready
- Streaming improves UX for long operations
- Environment variables crucial for security
- Consider costs when exposing BAML endpoints publicly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/orbruno) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
