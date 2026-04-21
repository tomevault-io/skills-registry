---
name: privy
description: 指导 Privy 登录与钱包流程集成，覆盖 Safe 地址派生、Safe 部署、三次签名、CLOB API 凭据与授权。Use when working on Privy auth/wallet integration, Safe derivation/deployment, trading session initialization, or when user mentions Privy/三次签名/Polymarket Safe/CLOB. Use when this capability is needed.
metadata:
  author: aquarius-wing
---

# Privy 交易会话集成

## 适用场景
- 集成 Privy 登录与钱包流程（EOA、Safe、三次签名）
- 调整交易会话初始化（Initialize Trading Session）
- 排查 Safe 部署、API 凭据、授权签名问题

## 关键参考
- 必读资料: [PRIVY.md](PRIVY.md)

## 快速流程
1. 获取 EOA（Privy 登录后从钱包列表中匹配）
2. 由 EOA 确定性派生 Safe 地址（无需部署即可展示）
3. 初始化交易会话并按顺序执行三次签名
4. Safe 未部署时触发部署签名
5. API 凭据派生失败时创建新凭据（签名）
6. 未授权时批量执行授权交易（签名）

## 实施检查清单
- [ ] EOA 获取逻辑稳定，未认证时返回 undefined
- [ ] Safe 地址派生使用确定性算法且可在 UI 展示
- [ ] 交易会话初始化按顺序执行并更新步骤状态
- [ ] Safe 部署前先检查链上/接口状态，失败可回退 RPC
- [ ] API 凭据先尝试派生，失败再创建
- [ ] 授权仅在未授权时触发，批量执行并处理失败
- [ ] 错误信息清晰，可用于定位签名失败环节

## 三次签名位置（要点）
- Safe 部署签名: `relayClient.deploy()`
- API 凭据签名: `tempClient.deriveApiKey()` / `tempClient.createApiKey()`
- 授权签名: `relayClient.execute(approvalTxs, ...)`

## 注意事项
- Safe 地址是确定性推导，部署前即可展示
- 只有在必要时触发签名，避免重复签名
- 部署与授权失败时要有明确错误输出，便于排查

## 实践经验：余额查询

**Privy Provider 与公共 RPC 的余额差异**：Privy 的 `getEthereumProvider()` 返回的 EIP-1193 provider 可能与公共 RPC（Alchemy、polygon-rpc 等）行为不一致，导致同一地址的链上只读查询（如 `balanceOf`）结果不同。

- **现象**：页面显示「Safe USDC.e 可用余额: 0」，但 `getWalletBalance`（使用 Alchemy RPC）返回 `usdcE: '3.972741'`。
- **根因**：用 Privy provider 调用 `getTokenBalance` / `eth_call` 时，可能因网络、RPC 配置或 Privy 内部行为返回 0。
- **建议**：
  - **只读余额展示**：使用服务端 `getWalletBalance`（Server Action，公共 RPC），与 wallet-card 等展示保持一致。
  - **需签名的流程**（提现、充值）：仍必须使用 Privy provider 发起交易；但余额校验可选用服务端 RPC 作为数据源，或接受二者可能存在短暂不一致。

## 输出规范
- 若改动流程或签名顺序，先更新 [PRIVY.md](PRIVY.md) 再修改代码

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aquarius-wing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
