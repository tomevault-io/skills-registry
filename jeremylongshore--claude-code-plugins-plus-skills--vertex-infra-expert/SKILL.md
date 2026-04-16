---
name: vertex-infra-expert
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---
# Vertex Infra Expert

## Overview

Provision Vertex AI infrastructure with Terraform (endpoints, deployed models, vector search indices, pipelines) with production guardrails: encryption, autoscaling, IAM least privilege, and operational validation steps. Use this skill to generate a minimal working Terraform baseline and iterate toward enterprise-ready deployments.

## Prerequisites

Before using this skill, ensure:
- Google Cloud project with Vertex AI API enabled
- Terraform 1.0+ installed
- gcloud CLI authenticated with appropriate permissions
- Understanding of Vertex AI services and ML models
- KMS keys created for encryption (if required)
- GCS buckets for model artifacts and embeddings

## Instructions

1. **Define AI Services**: Identify required Vertex AI components (endpoints, vector search, pipelines)
2. **Configure Terraform**: Set up backend and define project variables
3. **Provision Endpoints**: Deploy Gemini or custom model endpoints with auto-scaling
4. **Set Up Vector Search**: Create indices for embeddings with appropriate dimensions
5. **Configure Encryption**: Apply KMS encryption to endpoints and data
6. **Implement Monitoring**: Set up Cloud Monitoring for model performance
7. **Apply IAM Policies**: Grant least privilege access to AI services
8. **Validate Deployment**: Test endpoints and verify model availability

## Output


- Configuration files or code changes applied to the project
- Validation report confirming correct implementation
- Summary of changes made and their rationale

See [Terraform implementation details](${CLAUDE_SKILL_DIR}/references/implementation.md) for output format specifications.

## Error Handling

See `${CLAUDE_SKILL_DIR}/references/errors.md` for comprehensive error handling.

## Examples

See `${CLAUDE_SKILL_DIR}/references/examples.md` for detailed examples.

## Resources

- Vertex AI Terraform: https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/vertex_ai_endpoint
- Vertex AI documentation: https://cloud.google.com/vertex-ai/docs
- Model Garden: https://cloud.google.com/model-garden
- Vector Search guide: https://cloud.google.com/vertex-ai/docs/vector-search
- Terraform examples in ${CLAUDE_SKILL_DIR}/vertex-examples/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
