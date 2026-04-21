---
name: polymarket-privy-safe-builder
description: 规范 Polymarket 的 Privy + Safe + Builder + CLOB 集成流程，覆盖新老用户分支、三次签名、授权批处理、安全边界与排障步骤。Use when implementing or debugging Privy embedded wallet, Safe deployment, builder relayer, remote signing, CLOB api credentials, token approvals, or builder attribution order flow. Use when this capability is needed.
metadata:
  author: aquarius-wing
---

# Polymarket Privy Safe Builder 集成 Skill

## 适用场景
- 新建或重构 Privy 登录后的交易会话初始化流程
- 排查 `relayClient.deploy` / `deriveApiKey` / `execute approvals` 失败
- 实现 Builder attribution 与 gasless 下单
- 审核远程签名与用户凭据存储的安全边界

## 快速使用
1. 先识别用户路径：新用户（需部署/授权）或老用户（可能跳过步骤）。
2. 按“固定顺序”编排初始化，避免并发导致状态错乱。
3. 对每一步做可观测日志（输入、输出、耗时、失败原因）。
4. 完成后核对“验收清单”并执行最小可行联调。

## 固定初始化顺序（必须）
1. Privy 登录并拿到 EOA signer（ethers v5）。
2. 初始化 `RelayClient`（带 `BuilderConfig(remoteBuilderConfig.url)`）。
3. 由 EOA 确定性派生 Safe 地址。
4. 检查 Safe 是否已部署；未部署则执行 `relayClient.deploy()`。
5. 获取用户 CLOB API 凭据：先 `deriveApiKey()`，失败再 `createApiKey()`。
6. 检查授权状态；未授权时批量 `relayClient.execute(approvalTxs, ...)`。
7. 用 `credentials + signer + safeAddress + signatureType=2 + builderConfig` 初始化认证 `ClobClient`。
8. 才允许创建/撤销订单。

## 新老用户分支
- **新用户**：通常会触发 4/5/6 三段签名（部署、凭据、授权）。
- **老用户**：优先复用已部署 Safe 与已有授权，仅在必要时补签名。

## 关键实现约束
- 使用 `ethers` **v5** signer 贯穿 Relay/CLOB 调用。
- Safe 地址是确定性派生，部署前也可用于展示。
- 授权逻辑必须“先查后设”，避免重复授权与多余签名。
- `ClobClient` 仅在交易会话完成后创建，避免半初始化状态下发单。

## 安全红线
- Builder `secret` 仅在服务端使用，不进入客户端 bundle。
- 避免把完整用户 API credentials 长期明文存入 localStorage。
- 若必须做前端远程签名，至少要求已认证会话并校验调用来源。
- 生产优先使用服务端代理 CLOB/Relay 请求，减少密钥暴露面。

## 排障顺序
1. 环境变量是否齐全（Privy App ID、Polygon RPC、Builder 三元组）。
2. `/api/polymarket/sign` 是否可用、返回字段是否完整。
3. Safe 部署检查结果是否与链上状态一致。
4. `deriveApiKey` 失败是否正确 fallback 到 `createApiKey`。
5. 授权检查阈值与 spender/operator 地址是否正确。
6. 下单前 `ClobClient` 参数（`signatureType=2`、`funder=safeAddress`）是否正确。

## 最小验收清单
- [ ] 未登录用户不会触发任何链上签名流程
- [ ] 新用户首次初始化可完整走通并成功下单
- [ ] 老用户二次登录可跳过已完成步骤
- [ ] 授权只在未授权时触发且为批量执行
- [ ] 错误信息可区分部署失败/凭据失败/授权失败
- [ ] 敏感凭据不经日志明文输出

## 输出格式（执行该 skill 时）
- 先给“当前路径判断”（新用户/老用户）。
- 再给“步骤状态表”（每步: done/skip/fail + 原因）。
- 最后给“阻塞项与下一步动作”。

## 附加资料（一个场景一个文件）
- 场景索引: [reference.md](reference.md)
- 场景 1（Privy 登录与 signer）: [references/01-privy-auth-signer.md](references/01-privy-auth-signer.md)
- 场景 2（远程签名 API）: [references/02-builder-remote-signing.md](references/02-builder-remote-signing.md)
- 场景 3（RelayClient 与 Safe 部署）: [references/03-relay-safe-deployment.md](references/03-relay-safe-deployment.md)
- 场景 4（用户 API 凭据）: [references/04-user-api-credentials.md](references/04-user-api-credentials.md)
- 场景 5（Token 授权批处理）: [references/05-token-approvals.md](references/05-token-approvals.md)
- 场景 6（认证 ClobClient 初始化）: [references/06-authenticated-clob-client.md](references/06-authenticated-clob-client.md)
- 场景 7（下单与撤单）: [references/07-order-placement-cancel.md](references/07-order-placement-cancel.md)
- 场景 8（排障与回归检查）: [references/08-troubleshooting-playbook.md](references/08-troubleshooting-playbook.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aquarius-wing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
