---
name: klingai-storage-integration
description: | Use when this capability is needed.
metadata:
  author: helixdevelopment
---

# Klingai Storage Integration

## Overview

This skill demonstrates how to download and store generated videos in cloud storage services including AWS S3, Google Cloud Storage, and Azure Blob Storage.

## Prerequisites

- Kling AI API key configured
- Cloud storage credentials (AWS, GCP, or Azure)
- Python 3.8+ with cloud SDKs installed

## Instructions

Follow these steps to integrate storage:

1. **Configure Storage**: Set up cloud storage credentials
2. **Download Video**: Fetch generated video from Kling AI
3. **Upload to Cloud**: Store in your preferred provider
4. **Generate URLs**: Create access URLs (signed or public)
5. **Clean Up**: Remove temporary files

## Output

Successful execution produces:
- Downloaded video from Kling AI
- Uploaded to cloud storage
- Metadata preserved with video
- Signed URLs for secure access

## Error Handling

See `{baseDir}/references/errors.md` for comprehensive error handling.

## Examples

See `{baseDir}/references/examples.md` for detailed examples.

## Resources

- [AWS S3 SDK](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html)
- [Google Cloud Storage](https://cloud.google.com/storage/docs)
- [Azure Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/helixdevelopment) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
