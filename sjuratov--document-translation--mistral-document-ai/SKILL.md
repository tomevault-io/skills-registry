---
name: mistral-document-ai
description: Use Mistral Document AI (mistral-document-ai-2505) deployed on Azure AI Foundry for OCR of PDF and image documents. This skill provides Python code patterns for authenticating and calling the REST API endpoint to extract text and structured data from documents. Use when this capability is needed.
metadata:
  author: sjuratov
---

# Mistral Document AI

Mistral Document AI (model: `mistral-document-ai-2505`) is deployed on Azure AI Foundry and accessed via REST API for OCR and document understanding.

## Authentication & Endpoint

**Authentication**: API Key (Bearer token)
**Endpoint format**: `https://<endpoint_name>.services.ai.azure.com/providers/mistral/azure/ocr`
**Model**: `mistral-document-ai-2505`
**Important**: Only base64-encoded content is supported, no document URLs.

## Environment Setup

```python
import os
import base64
import requests
from typing import Optional, Dict, Any

# Configuration
MISTRAL_ENDPOINT = os.getenv("MISTRAL_DOCUMENT_AI_ENDPOINT")  
MISTRAL_API_KEY = os.getenv("MISTRAL_DOCUMENT_AI_KEY")
```

Environment variables:
- `MISTRAL_DOCUMENT_AI_ENDPOINT`: Full endpoint URL (e.g., `https://nextgen-project-1-resource.services.ai.azure.com/providers/mistral/azure/ocr`)
- `MISTRAL_DOCUMENT_AI_KEY`: API key for authentication

## Core OCR Function

```python
def ocr_document(
    file_path: str,
    include_image_base64: bool = False,
    document_annotation_format: Optional[Dict[str, Any]] = None
) -> Dict[str, Any]:
    """
    Extract text from PDF or image using Mistral Document AI.
    
    Args:
        file_path: Path to PDF or image file
        include_image_base64: Include base64 images in response
        document_annotation_format: Optional structured output schema
        
    Returns:
        OCR response with extracted text and metadata
    """
    # Read and encode file
    with open(file_path, 'rb') as f:
        file_content = base64.b64encode(f.read()).decode('utf-8')
    
    # Determine content type
    if file_path.lower().endswith('.pdf'):
        content_type = 'application/pdf'
        doc_type = 'document_url'
        key = 'document_url'
    else:
        # Supports: jpeg, jpg, png, gif, bmp, tiff, webp
        ext = file_path.lower().split('.')[-1]
        content_type = f'image/{ext}' if ext != 'jpg' else 'image/jpeg'
        doc_type = 'image_url'
        key = 'image_url'
    
    # Build request payload
    payload = {
        "model": "mistral-document-ai-2505",
        "document": {
            "type": doc_type,
            key: f"data:{content_type};base64,{file_content}"
        },
        "include_image_base64": include_image_base64
    }
    
    # Add optional structured output
    if document_annotation_format:
        payload["document_annotation_format"] = document_annotation_format
    
    # Make API request
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {MISTRAL_API_KEY}"
    }
    
    response = requests.post(
        MISTRAL_ENDPOINT,
        headers=headers,
        json=payload,
        timeout=120
    )
    
    response.raise_for_status()
    return response.json()
```

## Usage Patterns

### Basic OCR (Extract All Text)

```python
result = ocr_document("document.pdf")
extracted_text = result['choices'][0]['message']['content']
```

### OCR with Structured Output

Extract specific fields using JSON schema:

```python
annotation_schema = {
    "type": "json_schema",
    "json_schema": {
        "schema": {
            "type": "object",
            "properties": {
                "language": {"type": "string", "description": "Detected language"},
                "title": {"type": "string", "description": "Document title"},
                "summary": {"type": "string", "description": "Brief summary"}
            },
            "required": ["language", "title", "summary"],
            "additionalProperties": False
        },
        "name": "document_annotation",
        "strict": True
    }
}

result = ocr_document("document.pdf", document_annotation_format=annotation_schema)
structured_data = result['choices'][0]['message']['content']
```

### Process Specific PDF Pages

Extract pages first, then OCR each page:

```python
import fitz  # PyMuPDF

def ocr_pdf_pages(pdf_path: str, pages: list[int]) -> dict[int, str]:
    """OCR specific pages from a PDF."""
    results = {}
    doc = fitz.open(pdf_path)
    
    for page_num in pages:
        # Extract single page as new PDF
        single_page_doc = fitz.open()
        single_page_doc.insert_pdf(doc, from_page=page_num, to_page=page_num)
        
        # Save to temporary bytes
        temp_pdf = single_page_doc.write()
        temp_path = f"temp_page_{page_num}.pdf"
        with open(temp_path, 'wb') as f:
            f.write(temp_pdf)
        
        # OCR the single page
        result = ocr_document(temp_path)
        results[page_num] = result['choices'][0]['message']['content']
        
        # Cleanup
        os.remove(temp_path)
        single_page_doc.close()
    
    doc.close()
    return results
```

## Response Structure

```python
{
    "id": "request_id",
    "object": "chat.completion",
    "created": 1234567890,
    "model": "mistral-document-ai-2505",
    "choices": [
        {
            "index": 0,
            "message": {
                "role": "assistant",
                "content": "Extracted text or structured JSON..."
            },
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "prompt_tokens": 123,
        "completion_tokens": 456,
        "total_tokens": 579
    }
}
```

## Error Handling

```python
def ocr_document_safe(file_path: str) -> Optional[str]:
    """OCR with error handling."""
    try:
        result = ocr_document(file_path)
        return result['choices'][0]['message']['content']
    except requests.exceptions.HTTPError as e:
        if e.response.status_code == 401:
            raise ValueError("Invalid API key")
        elif e.response.status_code == 429:
            raise ValueError("Rate limit exceeded")
        else:
            raise ValueError(f"OCR failed: {e.response.text}")
    except Exception as e:
        raise ValueError(f"OCR error: {str(e)}")
```

## Dependencies

```toml
# Add to pyproject.toml
dependencies = [
    "requests>=2.31.0",
    "pymupdf>=1.23.0",  # For PDF page extraction
]
```

## Best Practices

1. **File size limits**: Test with your specific endpoint's file size limits
2. **Timeout**: Adjust timeout based on document size (larger PDFs need more time)
3. **Rate limiting**: Implement retry logic with exponential backoff
4. **Environment variables**: Never hardcode API keys
5. **Page-by-page processing**: For large PDFs, process pages individually to avoid timeouts
6. **Structured output**: Use JSON schemas when you need specific fields extracted

## Integration with LangChain Agents

```python
from langchain.tools import Tool

def create_ocr_tool():
    """Create a LangChain tool for OCR."""
    return Tool(
        name="ocr_document",
        description="Extract text from PDF or image documents using Mistral Document AI OCR",
        func=lambda file_path: ocr_document_safe(file_path)
    )
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sjuratov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
