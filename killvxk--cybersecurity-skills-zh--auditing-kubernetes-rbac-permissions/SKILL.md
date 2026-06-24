---
name: auditing-kubernetes-rbac-permissions
description: Kubernetes 基于角色的访问控制（RBAC）审计，系统性地审查角色、集群角色、绑定关系及服务账户权限，以识别过度宽松的访问配置、权限提升路径及违反最小权限原则的情况。 Use when this capability is needed.
metadata:
  author: killvxk
---
# 审计 Kubernetes RBAC 权限

## 概述

Kubernetes 基于角色的访问控制（RBAC）审计系统性地审查角色、集群角色、绑定关系及服务账户权限，以识别过度宽松的访问配置、权限提升路径及违反最小权限原则的情况。rbac-tool、KubiScan 和 rakkess 等工具可自动发现危险的权限组合。

## 前置条件

- 启用 RBAC 的 Kubernetes 集群（自 1.6 版本起为默认配置）
- 具有 cluster-admin 访问权限的 kubectl 以进行完整审计
- 已安装 rbac-tool、rakkess 或 KubiScan

## 核心概念

### RBAC 组件

| 资源 | 作用域 | 用途 |
|----------|-------|---------|
| Role | 命名空间 | 授予命名空间内的权限 |
| ClusterRole | 集群 | 授予集群范围的权限 |
| RoleBinding | 命名空间 | 在命名空间内将 Role/ClusterRole 绑定到主体 |
| ClusterRoleBinding | 集群 | 将 ClusterRole 绑定到集群范围的主体 |

### 危险权限组合

| 权限 | 风险等级 | 影响 |
|-----------|------|--------|
| `*` on `*` 资源 | 严重 | 等同于 cluster-admin |
| create pods | 高 | 可部署特权 Pod |
| create pods/exec | 高 | 可 exec 进入任意 Pod |
| get secrets | 高 | 可读取所有 Secret |
| create clusterrolebindings | 严重 | 可提升至 cluster-admin |
| impersonate users | 严重 | 可伪装成任意用户 |
| escalate on roles | 严重 | 可授予超出自身的权限 |
| bind on roles | 高 | 可创建新的角色绑定 |

## 实施步骤

### 步骤 1：枚举所有 RBAC 资源

```bash
# 列出所有 ClusterRole
kubectl get clusterroles -o name | wc -l
kubectl get clusterroles --no-headers | grep -v "system:"

# 列出所有 ClusterRoleBinding
kubectl get clusterrolebindings -o wide

# 按命名空间列出所有 Role
kubectl get roles -A

# 按命名空间列出所有 RoleBinding
kubectl get rolebindings -A -o wide

# 导出所有 RBAC 以进行离线分析
kubectl get clusterroles,clusterrolebindings,roles,rolebindings -A -o yaml > rbac-export.yaml
```

### 步骤 2：识别通配符权限

```bash
# 查找对所有资源具有通配符动词的 ClusterRole
kubectl get clusterroles -o json | jq -r '
  .items[] |
  select(.rules[]? |
    (.verbs | index("*")) and
    (.resources | index("*"))
  ) |
  .metadata.name'

# 查找可以创建 Pod 的角色
kubectl get clusterroles -o json | jq -r '
  .items[] |
  select(.rules[]? |
    (.verbs | index("create") or index("*")) and
    (.resources | index("pods") or index("*"))
  ) |
  .metadata.name'

# 查找可以读取 Secret 的角色
kubectl get clusterroles -o json | jq -r '
  .items[] |
  select(.rules[]? |
    (.verbs | index("get") or index("list") or index("*")) and
    (.resources | index("secrets") or index("*"))
  ) |
  .metadata.name'
```

### 步骤 3：检查服务账户权限

```bash
# 列出所有服务账户
kubectl get serviceaccounts -A

# 检查默认服务账户的权限
for ns in $(kubectl get ns -o jsonpath='{.items[*].metadata.name}'); do
  echo "=== $ns/default ==="
  kubectl auth can-i --list --as=system:serviceaccount:$ns:default 2>/dev/null | grep -v "no"
done

# 检查具有 cluster-admin 的服务账户
kubectl get clusterrolebindings -o json | jq -r '
  .items[] |
  select(.roleRef.name == "cluster-admin") |
  {binding: .metadata.name, subjects: [.subjects[]? | {kind, name, namespace}]}'
```

### 步骤 4：使用 rbac-tool 进行自动分析

```bash
# 安装 rbac-tool
kubectl krew install rbac-tool

# 可视化 RBAC
kubectl rbac-tool viz --outformat dot | dot -Tpng > rbac-graph.png

# 查找谁可以执行特定操作
kubectl rbac-tool who-can get secrets -A
kubectl rbac-tool who-can create pods -A
kubectl rbac-tool who-can '*' '*'

# 分析所有权限
kubectl rbac-tool analysis

# 生成 RBAC 策略报告
kubectl rbac-tool auditgen > rbac-audit.yaml
```

### 步骤 5：检查权限提升路径

```bash
# 检查是否有角色可以提升权限
kubectl get clusterroles -o json | jq -r '
  .items[] |
  select(.rules[]? |
    (.verbs | index("escalate") or index("bind") or index("impersonate")) and
    (.resources | index("clusterroles") or index("roles") or index("clusterrolebindings") or index("rolebindings") or index("users") or index("groups") or index("serviceaccounts"))
  ) |
  .metadata.name'

# 检查伪装权限
kubectl get clusterroles -o json | jq -r '
  .items[] |
  select(.rules[]? |
    (.verbs | index("impersonate"))
  ) |
  {name: .metadata.name, rules: .rules}'
```

### 步骤 6：使用 KubiScan 审计

```bash
# 安装 KubiScan
pip install kubiscan

# 查找有风险的角色
kubiscan --risky-roles

# 查找有风险的 ClusterRole
kubiscan --risky-clusterroles

# 查找有风险的主体
kubiscan --risky-subjects

# 查找具有有风险服务账户的 Pod
kubiscan --risky-pods

# 完整报告
kubiscan --all
```

## 验证命令

```bash
# 验证特定权限
kubectl auth can-i create pods --as=system:serviceaccount:default:myapp

# 检查用户的所有权限
kubectl auth can-i --list --as=developer@example.com

# 使用 kubescape 验证 RBAC
kubescape scan framework nsa --controls-config rbac-controls.json

# 测试最小权限
kubectl auth can-i delete nodes --as=system:serviceaccount:app:web-server
# 预期结果: no
```

## 参考资料

- [Kubernetes RBAC 文档](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [rbac-tool GitHub](https://github.com/alcideio/rbac-tool)
- [KubiScan - 风险权限扫描器](https://github.com/cyberark/KubiScan)
- [CIS Kubernetes Benchmark - 第 5.1 节](https://www.cisecurity.org/benchmark/kubernetes)

---
> Source: [killvxk/cybersecurity-skills-zh](https://github.com/killvxk/cybersecurity-skills-zh) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
