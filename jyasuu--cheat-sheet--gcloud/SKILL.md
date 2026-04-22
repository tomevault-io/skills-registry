---
name: gcloud
description: Google Cloud CLI commands for authentication, clusters, services, and compute resources. Use when this capability is needed.
metadata:
  author: jyasuu
---

# gcloud

```bash
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-linux-x86_64.tar.gz
tar -xf google-cloud-cli-linux-x86_64.tar.gz
./google-cloud-sdk/install.sh
./google-cloud-sdk/bin/gcloud init
gcloud auth application-default login
gcloud container clusters get-credentials $cluster --region ? --project ?
gcloud components install gke-gcloud-auth-plugin
gcloud services enable compute.googleapis.com --project=?
gcloud compute addresses list
gcloud compute ssl-certificates list
gcloud compute ssl-certificates describe supabase-ssl-cert
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jyasuu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
