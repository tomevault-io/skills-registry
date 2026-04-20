---
name: ai-ml-storage-design
description: Design storage for AI/ML workloads on Google Cloud. specific for GKE/GCE deployments. Use this skill when the user asks about storage architecture for AI models, inference serving, or training data storage, or when deciding between Cloud Storage and Managed Lustre. Use when this capability is needed.
metadata:
  author: mastersingh24
---

# AI/ML Storage Design

This skill helps design storage architecture for AI and ML workloads on Google Cloud, covering both **Training** and **Serving (Inference)** phases.

## 0. Critical First Step: Check Existing Infrastructure

**Before proposing new infrastructure, ALWAYS ask the user if they have existing storage resources.** This prevents redundancy and leveraged existing investments.

**Ask:**
*   "Do you already have a **Managed Lustre** instance running for your training pipeline?"
*   "Are your datasets or models already stored in **Cloud Storage buckets**?"

**List Lustre instances:**
```bash
gcloud lustre instances list --location=<REGION>
```

**Decision Rule:**
*   **If Managed Lustre exists:** Strongly consider using it for both Training and Serving (especially for single-zone serving) to maintain consistency and performance.
*   **If GCS is the only existing storage:** GCS + FUSE is the default starting point unless performance requirements force a move to Lustre.

---

## 1. Storage Selection: Training

Training involves two main activities: **Data Loading** and **Checkpointing**.

### A. Data Loading (Reading Training Data)

| Feature | **Cloud Storage (GCS) + FUSE** | **Managed Lustre** |
| :--- | :--- | :--- |
| **Best For** | **Large files** (≥ 50 MB), Cost efficiency, Durability priority. | **Small files** (< 50 MB), Max performance, POSIX compliance. |
| **Latency** | Tens of milliseconds. | Ultra-low (< 1 ms). |
| **Throughput** | High (with FUSE + Anywhere Cache). | Extremely high. |

**Recommendation:**
*   **Use Managed Lustre if:** Files are small (< 50 MB), strict POSIX compliance is needed, or latency must be sub-millisecond.
*   **Use GCS + FUSE if:** Files are large (≥ 50 MB), or you prioritize durability/HA over raw random I/O speed.

### B. Checkpointing (Saving State)

**Recommendation:**
*   **Use Managed Lustre if:** You are already using it for data loading, or require frequent, high-performance checkpoints to minimize training pauses.
*   **Use GCS if:** You are using GCS FUSE for data loading. Use **Hierarchical Namespaces** on the bucket to speed up atomic renames and checkpoint saves.

---

## 2. Storage Selection: Serving (Inference)

When moving to production serving, the choice depends on your deployment architecture.

### A. Cloud Storage (GCS)
**Best for:** Scalable, multi-region serving with dynamic fleets.

**Recommendation:** Use **GCS + Cloud Storage FUSE + Anywhere Cache**.

**Select if ANY of the following apply:**
*   **Dynamic Scalability:** The number of inference nodes changes dynamically (autoscaling).
*   **Infrequent Updates:** Model files are updated infrequently.
*   **Multi-Region/Zone:** Serving models from multiple zones or regions.
*   **High Availability:** Priority is durability and availability against regional/zonal failures.

**Key Optimization:**
*   **Cloud Storage FUSE:** Enable [parallel downloads](https://docs.cloud.google.com/storage/docs/cloud-storage-fuse/file-caching#configure-parallel-downloads) for faster model loading.
*   **Anywhere Cache:** Essential for high throughput (>1 TB/s) or large fleets (>100 nodes).

### B. Managed Lustre
**Best for:** High-performance, single-zone, low-latency serving.

**Select if ANY of the following apply:**
*   **Existing Workflow:** Training/checkpointing already uses Managed Lustre (avoids data movement).
*   **Single Zone:** Serving is restricted to a single zone.
*   **Performance Sensitive:** Requires consistent, ultra-low latency (<1ms).
*   **Frequent Updates:** Model files are updated frequently.

### C. Implementation (GKE)
When using Managed Lustre on GKE, use the **Lustre CSI driver**.

**PV Example:**
```yaml
csi:
  driver: lustre.csi.storage.gke.io
  volumeHandle: <PROJECT>/<LOCATION>/<INSTANCE>
  volumeAttributes:
    filesystem: "models"
    ip: "10.x.x.x"
```

---

## Discovery Questions

If requirements are unclear, ask these questions (after checking for existing infra):

1.  **"What is the typical file size of your training data? Are they many small files (<50MB) or fewer large files?"** (Small -> Lustre; Large -> GCS)
2.  **"Are you serving the model in a single zone, or across multiple regions?"** (Multi-region -> GCS)
3.  **"Do you need to scale the number of inference nodes dynamically?"** (Yes -> GCS)
4.  **"How strict are your latency requirements for data loading? Do you need sub-millisecond access?"** (Yes -> Lustre)

## Optimization Tips
*   **Training:** For GCS, always use **Cloud Storage FUSE**. For high throughput requirements, enable **Anywhere Cache**.
*   **Model Load Time:** Optimize for rapid model loading to minimize accelerator (GPU/TPU) idle time.
*   **Archive:** Use GCS **Autoclass** or Lifecycle Management to automatically transition older training data/models to cheaper storage classes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mastersingh24) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
