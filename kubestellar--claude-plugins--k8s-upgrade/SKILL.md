---
name: k8s-upgrade
description: Upgrade cluster (master and nodes) Use when this capability is needed.
metadata:
  author: kubestellar
---

## Your task

Guide the user through a Kubernetes cluster upgrade with appropriate safety checks.

### CRITICAL SAFETY NOTES

- **ALWAYS** run prerequisite checks before any upgrade
- **NEVER** trigger an upgrade without explicit user confirmation
- **WARN** users about potential downtime and workload impact
- For managed clusters (EKS/GKE/AKS), only provide guidance - do not attempt direct upgrades

### Workflow

1. **Identify Target Cluster**
   - Use `list_clusters` to show available clusters
   - Ask the user which cluster to upgrade
   - Use `detect_cluster_type` to determine the distribution

2. **Check Current State**
   - Use `get_cluster_version_info` to show current version and available upgrades
   - If no upgrades available, inform the user and stop

3. **Select Target Version** (for OpenShift)
   - Present available upgrade versions to the user
   - Show upgrade graph with recommended paths if available
   - Ask user to confirm target version

4. **Run Prerequisites Check**
   - Use `get_upgrade_prerequisites` to verify:
     - All nodes are Ready
     - No pods in CrashLoopBackOff or ImagePullBackOff
     - No degraded ClusterOperators (OpenShift)
     - No MachineConfigPools updating (OpenShift)
   - If any checks fail, show issues and recommend fixing before upgrading

5. **Handle by Cluster Type**

   **OpenShift:**
   - Show exactly what will happen during the upgrade
   - Warn about expected behavior:
     - Rolling restart of control plane and workers
     - Temporary API unavailability during control plane upgrade
     - Workload disruptions during node drains
   - Request explicit confirmation: "Type 'yes-upgrade-now' to proceed"
   - Use `trigger_openshift_upgrade` with the confirmation
   - Use `get_upgrade_status` to monitor progress

   **EKS:**
   ```
   To upgrade EKS:
   1. Update control plane via AWS Console or:
      aws eks update-cluster-version --name <cluster> --kubernetes-version <version>
   2. Wait for control plane upgrade to complete
   3. Update node groups:
      aws eks update-nodegroup-version --cluster-name <cluster> --nodegroup-name <name>
   4. Update add-ons (VPC CNI, CoreDNS, kube-proxy):
      aws eks update-addon --cluster-name <cluster> --addon-name <addon>
   ```

   **GKE:**
   ```
   To upgrade GKE:
   1. Via Console: Container > Clusters > <cluster> > Upgrade available
   2. Via gcloud:
      gcloud container clusters upgrade <cluster> --master --cluster-version <version>
   3. Node pools upgrade separately:
      gcloud container clusters upgrade <cluster> --node-pool <pool> --cluster-version <version>
   ```

   **AKS:**
   ```
   To upgrade AKS:
   1. Via Portal: Kubernetes services > <cluster> > Upgrade
   2. Via CLI:
      az aks upgrade --resource-group <rg> --name <cluster> --kubernetes-version <version>
   ```

   **kubeadm:**
   ```
   To upgrade a kubeadm cluster:

   1. Upgrade control plane (on first control plane node):
      sudo apt-get update && sudo apt-get install -y kubeadm=<version>
      sudo kubeadm upgrade plan
      sudo kubeadm upgrade apply v<version>

   2. Upgrade additional control plane nodes:
      sudo kubeadm upgrade node

   3. Upgrade kubelet and kubectl on control plane nodes:
      sudo apt-get install -y kubelet=<version> kubectl=<version>
      sudo systemctl daemon-reload && sudo systemctl restart kubelet

   4. Upgrade worker nodes (one at a time):
      kubectl drain <node> --ignore-daemonsets --delete-emptydir-data
      # SSH to node:
      sudo apt-get update && sudo apt-get install -y kubeadm=<version>
      sudo kubeadm upgrade node
      sudo apt-get install -y kubelet=<version> kubectl=<version>
      sudo systemctl daemon-reload && sudo systemctl restart kubelet
      # Back on control plane:
      kubectl uncordon <node>
   ```

6. **Monitor Progress** (OpenShift only)
   - Use `get_upgrade_status` periodically to check progress
   - Report on ClusterOperator and MachineConfigPool status
   - Alert on any degraded operators or pools

### Available Tools

| Tool | Purpose |
|------|---------|
| `list_clusters` | Discover clusters |
| `detect_cluster_type` | Identify distribution type |
| `get_cluster_version_info` | Get version and upgrade options |
| `get_upgrade_prerequisites` | Validate upgrade readiness |
| `trigger_openshift_upgrade` | Initiate OpenShift upgrade (requires confirmation) |
| `get_upgrade_status` | Monitor upgrade progress |

### Confirmation Requirement

Before triggering any upgrade, you MUST:
1. Show the user exactly what will happen
2. List all prerequisites that passed/failed
3. Warn about potential impact:
   - API server may be temporarily unavailable
   - Nodes will be drained and rebooted
   - Workloads may experience disruption
4. Request explicit confirmation with the exact phrase "yes-upgrade-now"

Do not use any other tools besides the kubestellar-ops MCP tools.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubestellar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
