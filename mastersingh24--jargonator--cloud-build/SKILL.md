---
name: cloud-build-best-practices
description: Instructions for building container images using Google Cloud Build and cloudbuild.yaml. Use when this capability is needed.
metadata:
  author: mastersingh24
---

# Cloud Build Best Practices

Use Google Cloud Build for reliable, serverless container builds. Always define your build configuration in a `cloudbuild.yaml` file rather than relying on command-line flags alone.

## 1. Why `cloudbuild.yaml`?
- **Reproducibility**: Defines the exact steps, ensuring consistency across builds.
- **Version Control**: Can be committed to git, allowing history tracking of build configuration changes.
- **Complex Workflows**: Supports multiple steps, parallel execution, and secret management.

## 2. Standard `cloudbuild.yaml` Structure
A typical configuration for building a Docker image involved:
1.  **Build**: Running `docker build` (or using Kaniko/Buildpacks).
2.  **Push**: Pushing the image to a container registry (Artifact Registry or GCR).

## 3. Variable Substitution
Use substitutions to make your build configuration dynamic.
- `_IMAGE_NAME`: Custom variable for the image name.
- `$PROJECT_ID`: Built-in variable for the GCP Project ID.
- `$short_sha`: Built-in variable for the commit SHA (useful for tagging).

## Example `cloudbuild.yaml`

```yaml
steps:
  # Build the container image
  - name: 'gcr.io/cloud-builders/docker'
    args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/$_IMAGE_NAME:$SHORT_SHA', '-t', 'gcr.io/$PROJECT_ID/$_IMAGE_NAME:latest', '.' ]

# Push the images to Container Registry
images:
  - 'gcr.io/$PROJECT_ID/$_IMAGE_NAME:$SHORT_SHA'
  - 'gcr.io/$PROJECT_ID/$_IMAGE_NAME:latest'

# Define default substitution variable values
substitutions:
    _IMAGE_NAME: my-app
```

## 4. Execution
Run the build using the `gcloud` CLI:

```bash
gcloud builds submit --config cloudbuild.yaml .
```

To override substitutions at runtime:

```bash
gcloud builds submit --config cloudbuild.yaml --substitutions=_IMAGE_NAME=jargonator .
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mastersingh24) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
