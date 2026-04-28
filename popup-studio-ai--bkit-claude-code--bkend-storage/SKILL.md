---
name: bkend-storage
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# bkend.ai Storage Guide

## Upload Methods

| Method | Use Case | Process |
|--------|----------|---------|
| Single | Normal files | Presigned URL -> PUT upload -> Register metadata |
| Multiple | Multiple files | Repeat single upload |
| Multipart | Large files | Initialize -> Part URLs -> Complete |

## Presigned URL

- Validity: 15 minutes
- PUT method with file binary
- Content-Type header required

## File Visibility (4 levels)

| Level | Access | URL Type |
|-------|--------|----------|
| public | Anyone | CDN URL (no expiry) |
| private | Owner only | Presigned URL (1 hour) |
| protected | Authenticated users | Presigned URL (1 hour) |
| shared | Specified targets | Presigned URL (1 hour) |

## Size Limits

| Category | Max Size |
|----------|----------|
| Images | 10 MB |
| Videos | 100 MB |
| Documents | 20 MB |

## Storage Categories

images, documents, media, attachments

## MCP Storage Workflow

bkend MCP does NOT have dedicated storage tools. Use this workflow:

1. **Search docs**: `search_docs` with query "file upload presigned url"
2. **Get examples**: `search_docs` with query "file upload code examples"
3. **Generate code**: AI generates REST API code for file operations

### Searchable Storage Docs
| Doc ID | Content |
|--------|---------|
| `7_code_examples_data` | CRUD + file upload code examples |

## REST Storage API

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /v1/files/presigned-url | Generate presigned URL |
| POST | /v1/files | Register metadata (complete upload) |
| GET | /v1/files | File list |
| GET | /v1/files/:fileId | File detail |
| PATCH | /v1/files/:fileId | Update metadata |
| DELETE | /v1/files/:fileId | Delete file |
| POST | /v1/files/:fileId/download-url | Generate download URL |

## Multipart Upload (Large Files)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /v1/files/multipart/init | Initialize multipart upload |
| POST | /v1/files/multipart/presigned-url | Get part upload URL |
| POST | /v1/files/multipart/complete | Complete multipart upload |
| POST | /v1/files/multipart/abort | Abort multipart upload |

## Upload Flow (Single File)

```
1. POST /v1/files/presigned-url -> { url, fileId }
2. PUT {url} with file binary + Content-Type header
3. POST /v1/files with { fileId, filename, contentType, size, visibility }
```

## Multipart Upload Flow (Large File)

```
1. POST /v1/files/multipart/init -> { uploadId }
2. POST /v1/files/multipart/presigned-url -> [{ partNumber, url }]
3. PUT each part URL with file chunk
4. POST /v1/files/multipart/complete -> { file }
```

## Official Documentation (Live Reference)

For the latest storage documentation, use WebFetch:
- Storage Overview: https://raw.githubusercontent.com/popup-studio-ai/bkend-docs/main/en/storage/01-overview.md
- MCP Storage Guide: https://raw.githubusercontent.com/popup-studio-ai/bkend-docs/main/en/mcp/07-storage-tools.md
- Full TOC: https://raw.githubusercontent.com/popup-studio-ai/bkend-docs/main/SUMMARY.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
