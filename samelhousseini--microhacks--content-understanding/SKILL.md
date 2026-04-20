---
name: content-understanding
description: Process multimodal content (documents, images, audio, video) using Azure AI Content Understanding. Use when extracting structured data from invoices, receipts, IDs, analyzing media files, or building multimodal content pipelines. Use when this capability is needed.
metadata:
  author: samelhousseini
---

# Content Understanding Skill

## Folder Contents

| File | Type | Description |
|------|------|-------------|
| `SKILL.md` | Documentation | Main skill documentation with API reference, quick start, and patterns |
| `PRD.md` | Documentation | Product Requirements Document for the skill |
| `.env.sample` | Configuration | Sample environment variables template |
| `requirements.txt` | Dependencies | Python package dependencies |
| **scripts/** | | |
| `scripts/__init__.py` | Module | Package initializer with exports |
| `scripts/content_understanding_client.py` | Client | Core API client for Content Understanding service with analyze, poll, and create methods |
| `scripts/invoice_analyzer.py` | Analyzer | Invoice/receipt field extraction with structured output (vendor, totals, line items) |
| `scripts/media_analyzer.py` | Analyzer | Audio/video analysis with transcription and speaker identification |
| `scripts/custom_analyzer.py` | Analyzer | Create and manage custom analyzers with extracted and AI-generated fields |

---

Transform unstructured documents, images, audio, and video into structured, searchable data using Azure AI Content Understanding (GA November 2025, API version 2025-11-01).

## Overview

Azure AI Content Understanding is a core component of Microsoft Foundry Tools that provides:
- **Multimodal Processing**: Documents, images, audio, and video
- **14 Prebuilt Analyzers**: OCR, layout, invoices, receipts, IDs, audio/video search
- **Custom Analyzers**: Domain-specific field extraction with AI-generated fields
- **RAG Integration**: Optimized for search and retrieval scenarios

## Supported Content Types

| Modality | Formats | Max Size | Max Duration/Pages |
|----------|---------|----------|-------------------|
| **Documents** | PDF, TIFF, DOCX, XLSX, PPTX, TXT, HTML, MD, EML | 200 MB | 300 pages |
| **Images** | JPG, PNG, BMP, HEIF, HEIC | 200 MB | 10,000 x 10,000 px |
| **Audio** | WAV, MP3, MP4, OPUS, OGG, FLAC, AAC | 1 GB | 4 hours |
| **Video** | MP4, M4V, FLV, WMV, AVI, MKV, MOV | 4 GB (URL) | 2 hours |

## Prebuilt Analyzers

### Content Extraction
- `prebuilt-read` - Basic OCR, words, paragraphs, barcodes
- `prebuilt-layout` - Enhanced structure with tables, figures, annotations

### RAG/Search Analyzers
- `prebuilt-documentSearch` - Document ingestion with markdown output
- `prebuilt-imageSearch` - Image description and visual content
- `prebuilt-audioSearch` - Transcription with speaker labeling
- `prebuilt-videoSearch` - Keyframes, chapters, transcripts

### Domain-Specific Analyzers
- `prebuilt-invoice` - Invoices, utility bills, purchase orders
- `prebuilt-receipt` - Receipts with itemization
- `prebuilt-idDocument` - Driver's licenses, passports, IDs
- `prebuilt-contract` - Legal contracts
- US tax forms (1040, 1099, W-2, etc.)
- US mortgage documents

## Quick Start

### Environment Variables
```bash
AZURE_AI_ENDPOINT=https://your-foundry-name.services.ai.azure.com/
AZURE_AI_API_KEY=your-api-key-here
API_VERSION=2025-11-01
```

### Basic Usage
```python
from content_understanding_client import ContentUnderstandingClient

client = ContentUnderstandingClient()

# Analyze an invoice
result = client.analyze("prebuilt-invoice", "https://example.com/invoice.pdf")

# Extract fields
for content in result.get("result", {}).get("contents", []):
    fields = content.get("fields", {})
    print(f"Vendor: {fields.get('VendorName', {}).get('valueString', 'N/A')}")
    print(f"Total: {fields.get('InvoiceTotal', {}).get('valueNumber', 'N/A')}")
```

## Building Block Scripts

| Script | Description |
|--------|-------------|
| `content_understanding_client.py` | Core client for Content Understanding API |
| `invoice_analyzer.py` | Invoice/receipt extraction with structured output |
| `media_analyzer.py` | Audio/video analysis with transcription |
| `custom_analyzer.py` | Create and manage custom analyzers |

## Custom Analyzer Creation

Create domain-specific analyzers with extracted and AI-generated fields:

```python
custom_definition = {
    "description": "Purchase order analyzer",
    "baseAnalyzerId": "prebuilt-document",
    "models": {
        "completion": "gpt-4.1",
        "embedding": "text-embedding-3-large"
    },
    "fieldSchema": {
        "fields": {
            "PONumber": {
                "type": "string",
                "method": "extract",
                "description": "Purchase order number"
            },
            "UrgencyLevel": {
                "type": "string",
                "method": "generate",  # AI-inferred field
                "description": "Inferred urgency: Low, Medium, High"
            }
        }
    }
}

client.create_analyzer("my-po-analyzer", custom_definition)
```

## API Patterns

### Analyze Content (Async)
```python
# Start analysis
response = requests.post(
    f"{endpoint}/contentunderstanding/analyzers/{analyzer_id}:analyze",
    params={"api-version": "2025-11-01"},
    headers=headers,
    json={"inputs": [{"url": file_url}]}
)

# Poll for results
operation_location = response.headers.get("Operation-Location")
while True:
    result = requests.get(operation_location, headers=headers).json()
    if result["status"] in ["Succeeded", "Failed"]:
        break
    time.sleep(2)
```

### Set Default Model Deployments
```bash
curl -X PATCH "${AZURE_AI_ENDPOINT}/contentunderstanding/defaults?api-version=2025-11-01" \
  -H "Ocp-Apim-Subscription-Key: ${AZURE_AI_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
        "modelDeployments": {
          "gpt-4.1": "gpt-4.1",
          "gpt-4.1-mini": "gpt-4.1-mini",
          "text-embedding-3-large": "text-embedding-3-large"
        }
      }'
```

## Azure Resource Setup

### Deploy via Azure CLI
```bash
# Create AI Foundry resource (required for Content Understanding)
az cognitiveservices account create \
  --name $FOUNDRY_NAME \
  --resource-group $RESOURCE_GROUP \
  --kind AIServices \
  --sku S0 \
  --location eastus \
  --yes

# Get endpoint and key
az cognitiveservices account show \
  --name $FOUNDRY_NAME \
  --resource-group $RESOURCE_GROUP \
  --query "properties.endpoint" -o tsv

az cognitiveservices account keys list \
  --name $FOUNDRY_NAME \
  --resource-group $RESOURCE_GROUP \
  --query "key1" -o tsv
```

### Required Model Deployments
Deploy these models in Azure AI Foundry Portal:
- `gpt-4.1` - For invoice, receipt, document analyzers
- `gpt-4.1-mini` - For search analyzers
- `text-embedding-3-large` - For all analyzers

## Pricing

| Component | Pricing Model |
|-----------|--------------|
| **Content Extraction** | Per page (documents), per image, per minute (audio/video) |
| **Field Extraction** | Token-based (GPT-4o pricing) |
| **Contextualization** | Per content unit |

## Related Resources

- [Azure AI Content Understanding Documentation](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/)
- [azure-ai-content-understanding-python](https://github.com/Azure-Samples/azure-ai-content-understanding-python) - Official Python samples
- [azure-ai-search-with-content-understanding-python](https://github.com/Azure-Samples/azure-ai-search-with-content-understanding-python) - RAG solution

## Lessons Learned

### API Response Structure
Results are returned in a nested structure:
```python
result["result"]["contents"][0]["fields"]["FieldName"]["valueString"]
```

### Polling Pattern
Always implement proper polling with status checks:
- `Succeeded` - Results ready
- `Failed` - Check error message
- Other statuses - Keep polling

### File Input Options
- URL: Direct public URL or SAS-signed blob URL
- Base64: For inline content (documents only)
- Blob storage integration requires SAS token generation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samelhousseini) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
