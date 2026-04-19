---
name: vegas-backtest-optimizer
description: Optimize the Vegas 4H backtest loop (cargo run + MySQL back_test_log/strategy_config) by iteratively tweaking strategy_config/risk_config parameters, rerunning cargo, and selecting configs with win_rate at least 50 percent and positive profit. Use when automating Vegas backtest tuning in this repo with the provided MySQL docker and cargo run entrypoint. Use when this capability is needed.
metadata:
  author: fairwic
---

# Vegas Backtest Optimizer

## 快速流程（默认命令）

### 0) 常用环境变量（回测/实盘一致性）
- **回测入口**：`DB_HOST='mysql://root:example@localhost:33306/test?ssl-mode=DISABLED' cargo run`
- **预热K线数量（实盘预热对齐回测）**
  - `STRATEGY_WARMUP_LIMIT=500`：预热最小根数（默认 500）
  - `STRATEGY_WARMUP_LIMIT_MAX=10000`：预热最大上限（默认 10000）
  - 真实预热根数 = `max(STRATEGY_WARMUP_LIMIT, strategy.value.min_k_line_num)`，并受 MAX 限制
- **OKX 请求过期窗口（修复 expTime 过期）**
  - `OKX_REQUEST_EXPIRATION_MS=300000`（建议模拟盘/联调用 5 分钟）

### 1) 运行回测
```bash
cd /Users/mac2/onions/rust_quant && TIGHTEN_VEGAS_RISK=0 DB_HOST='mysql://root:example@localhost:33306/test?ssl-mode=DISABLED' cargo run
```
- `TIGHTEN_VEGAS_RISK=0`：禁用代码层强制风控收紧（推荐）
- 回测完成后可用分析脚本输出指标与收益曲线：
```bash
cd /Users/mac2/onions/rust_quant
source .venv/bin/activate
python scripts/vegas-backtest-analysis/scripts/visualize_backtest_detail.py \
  --db-host 127.0.0.1 --db-port 33306 --db-user root --db-password example \
  --db-name test --back-test-id <ID> --with-equity
# 生成 dist/vegas_backtest_detail_<ID>.png 和 dist/vegas_equity_<ID>.png
```

### 2) 查询最新回测结果
```bash
docker exec -i mysql mysql -uroot -pexample test -e "select id,win_rate,profit,final_fund,sharpe_ratio,annual_return,max_drawdown,volatility,created_at from back_test_log order by id desc limit 1\G"
```

### 2.1) 查询指定回测ID（例如基线 5692）
```bash
docker exec -i mysql mysql -uroot -pexample test -e "select id,strategy_type,inst_type,time,win_rate,profit,final_fund,sharpe_ratio,annual_return,max_drawdown,volatility,created_at from back_test_log where id=5692\\G"
```

### 3) 查看/更新策略配置（vegas 4H，id=11）
```bash
# 查看当前配置
docker exec -i mysql mysql -uroot -pexample test -e "select value,risk_config from strategy_config where id=11\G"

# 更新配置（使用JSON_OBJECT避免转义问题）
docker exec -i mysql mysql -uroot -pexample test -e 'UPDATE strategy_config SET value=JSON_OBJECT(...), risk_config=JSON_OBJECT(...) WHERE id=11;'
```

---

## 自动化调参建议

### 判优基线
- 基线通常指 `back_test_log` 中 **同市场、同周期、当前策略配置**的最优记录。
- 常用排序：`Sharpe ↓ → MaxDD ↑ → Profit ↓`（如果更偏收益，则用 `Profit ↓ → win_rate ↓`）。

### 代码改动守则（避免破坏基线）
- 只要“基线回测”的 `strategy_detail`（即策略配置 JSON）里 **出现某模块且 `is_open=true`**，就 **不允许删除该模块/字段/指标链路**（否则回放/复现会因反序列化丢字段而劣化）。
- 需要下线模块时，只能通过配置把它 **关闭**（例如 `is_open=false`、或权重设为 `0.0`），并保留最小实现以兼容旧配置。

### 自动化扫描流程（示意）
1. 更新 `strategy_config` 的 `value`（例如 LegDetection size/weight、MarketStructure 权重等）。
2. 启动回测：`cargo run`。
3. 解析日志里的 `back_test_id`（关键词：`回测日志保存成功`）。
4. 读取该 id 的 `back_test_log` 指标并与基线对比。
5. 保存最佳参数并回写到 `strategy_config`。

---

## 已验证的最佳配置

### 🏆 当前默认（2026-01-08，宽松极端K + 高波动降损）

**回测ID**: 5593（复现 5595）  
**性能**: win_rate≈57.3%, profit≈1752.6, Sharpe≈1.534, max_dd≈57.7%

**代码/开关**:
- ExtremeKFilter 默认宽松：实体≥0.65、波动≥1%、跨≥2 EMA。
- 高波动动态降损默认开启（振幅>5% 时 max_loss_percent 临时降至 0.045，可用 `DYNAMIC_MAX_LOSS=0` 关闭）。
- 其他保持基线：stop_loss 关，max_loss_percent=0.05。

### 历史配置

- 第一性原理v1（5001）：win_rate≈55.1%, profit≈+99.68, Sharpe≈0.264, max_dd≈65.4%
- 组合E：win_rate≈54.7%, profit≈+52.77, Sharpe≈0.14, max_dd≈73.5%

### 信号参数（value JSON）
```json
{
  "period": "4H",
  "min_k_line_num": 3600,
  "ema_signal": {
    "ema1_length": 12, "ema2_length": 144, "ema3_length": 169,
    "ema4_length": 576, "ema5_length": 676, "ema6_length": 2304, "ema7_length": 2704,
    "ema_breakthrough_threshold": 0.0032,
    "is_open": true
  },
  "volume_signal": {
    "volume_bar_num": 4, "volume_increase_ratio": 2.5, "volume_decrease_ratio": 2.5, "is_open": true
  },
  "ema_touch_trend_signal": {
    "ema1_with_ema2_ratio": 1.01, "ema2_with_ema3_ratio": 1.012,
    "ema3_with_ema4_ratio": 1.006, "ema4_with_ema5_ratio": 1.006, "ema5_with_ema7_ratio": 1.022,
    "price_with_ema_high_ratio": 1.0022,
    "price_with_ema_low_ratio": 0.9982,
    "is_open": true
  },
  "rsi_signal": { "rsi_length": 16, "rsi_oversold": 18.0, "rsi_overbought": 78.0, "is_open": true },
  "bolling_signal": { "period": 12, "multiplier": 2.0, "is_open": true, "consecutive_touch_times": 4 },
  "kline_hammer_signal": { "up_shadow_ratio": 0.6, "down_shadow_ratio": 0.6 },
  "signal_weights": {
    "weights": [
      ["SimpleBreakEma2through", 0.5], ["VolumeTrend", 0.4], ["EmaTrend", 0.35],
      ["Rsi", 0.6], ["Bolling", 0.55]
    ],
    "min_total_weight": 2.0
  }
}
```

### 风控参数（risk_config JSON）
```json
{
  "max_loss_percent": 0.06
}
```

---

## 近期优化记录（2026-01-07）

### MarketStructure 结论
- 结构信号在多轮回测中 avg_profit 为负，默认改为 **权重=0（仅采集）** 且 `enable_swing_signal=false`。
- 配置已支持渐进启用：`swing_threshold` / `internal_threshold` + `enable_swing_signal` / `enable_internal_signal`。

### LegDetection 结论
- **win_rate+profit 作为判优**：`back_test_id=5552`（size=7, weight=0.6）胜率 0.563、profit 1231.08。
- **Sharpe/回撤优先**：`back_test_id=5561`（size=7, weight=0.9）Sharpe 1.330、max_dd 0.489、profit 1335.56。
- 当前 `strategy_config` id=11 已指向 5561（Sharpe/回撤优先方案）。
- 细粒度扫描（size=6/8, weight=0.4/0.8）未超过 5552；size=7 weight=0.5/0.7 与 5552 相同。

---

## 🧪 第一性原理模块开发指南

### 模块开发原则

| 原则 | 说明 |
|------|------|
| **数据采集优先** | 新模块先作为数据采集（权重=0），验证有效后再调整权重 |
| **禁止信号覆盖** | 新信号不应直接覆盖原有权重系统的判断结果 |
| **过滤器谨慎启用** | 过滤器容易过滤掉有效信号，需精细调参后启用 |
| **增量验证** | 每次只改动一个模块，对比回测结果 |

### 新模块集成流程

```
1. 创建模块文件 → 2. 添加到mod.rs → 3. 集成到strategy.rs
   ↓                                      ↓
4. 权重设为0运行回测 → 5. 对比基线 → 6. 调整权重/启用过滤
```

### 已实现模块状态

| 模块 | 文件 | 状态 | 权重 | 说明 |
|------|------|------|------|------|
| 假突破检测 | `fake_breakout.rs` | ✅ | 0.0 | 检测假突破，仅数据采集 |
| EMA距离过滤 | `ema_filter.rs` | ⏸️ | - | 过滤逻辑禁用，需调参 |
| R系统止损 | `r_system.rs` | ⏸️ | - | 待集成到风控流程 |

### 待实现模块（第一性原理）

| 模块 | 优先级 | 说明 |
|------|--------|------|
| 分批止盈 | P1 | 40%/30%/30%分阶段止盈 |
| 时间止损 | P1 | 12/24/48 K线无盈利平仓 |
| 震荡市识别 | P2 | ADX<25识别震荡，调整参数 |
| 多周期共振 | P3 | 日线方向+4H入场区域+1H精确入场 |

---

## 调参经验总结

### ⚠️ 禁用的风控参数（已验证有害）
以下三个风控开关会导致频繁提前止损，严重损害收益：
- `is_used_signal_k_line_stop_loss`: false
- `is_one_k_line_diff_stop_loss`: false  
- `is_move_stop_open_price_when_touch_price`: false

**原因**：出场优化导致过早止损，在趋势策略中反而降低盈亏比。

### ⚠️ 禁用的新模块逻辑（已验证有害）
以下逻辑会破坏原有信号平衡：
- 假突破直接开仓（覆盖权重系统）→ profit: -40
- EMA距离过滤（阈值过严）→ 过滤有效信号
- 成交量递减过滤（阈值过严）→ 过滤有效信号

### ✅ 有效的新模块用法
- 假突破检测 + 权重=0 → profit: +99.68（**+89%**）

### ✅ 高影响因子（微调三因子）
| 参数 | 最佳值 | 调整方向 |
|------|--------|----------|
| `ema_breakthrough_threshold` | 0.0032 | ↑ 更严格，↓ 更宽松 |
| `price_with_ema_high_ratio` | 1.0022 | ↑ 更严格，↓ 更宽松 |
| `price_with_ema_low_ratio` | 0.9982 | ↓ 更严格，↑ 更宽松 |
| `min_total_weight` | 2.0 | ↑ 更严格，↓ 更宽松 |

### ⚡ 低影响因子
- `signal_weights` 中各信号权重：调整幅度0.1~0.3对结果影响不大
- `min_total_weight` 在 2.0~2.2 范围内结果相同
- RSI/Volume 参数：在合理范围内微调影响较小

---

## JSON 配置坑

### 实盘一致性开关（services 层）
- `LIVE_ATTACH_TP=1`：下单时附带止盈（默认不附带）
- `LIVE_CLOSE_OPPOSITE_POSITION=1`：反向持仓先平仓再开仓
- `LIVE_SKIP_IF_SAME_SIDE_POSITION=1`：已有同向持仓则跳过开新仓

### 必须包含的字段
- `kline_hammer_signal`：必须存在，否则解析失败

### SignalType 枚举正确值
```
SimpleBreakEma2through, VolumeTrend, EmaTrend, Rsi, TrendStrength,
EmaDivergence, PriceLevel, Bolling, Engulfing, KlineHammer,
LegDetection, MarketStructure, FairValueGap, EqualHighLow, PremiumDiscount,
FakeBreakout  # 新增
```
❌ 错误：`SimpleBreakEma2`（缺少 `through`）

### BasicRiskStrategyConfig 字段名
```rust
max_loss_percent: f64,
is_used_signal_k_line_stop_loss: Option<bool>,  // 信号K线止损
is_one_k_line_diff_stop_loss: Option<bool>,     // 1R止损
is_move_stop_open_price_when_touch_price: Option<bool>,  // 保本触发
atr_take_profit_ratio: Option<f64>,
fixed_signal_kline_take_profit_ratio: Option<f64>,
is_counter_trend_pullback_take_profit: Option<bool>,
```

---

## 🧪 OKX 模拟盘下单联调（E2E 流程）

### 运行方式（默认 ignore，需要显式开启）
```bash
cd /Users/mac2/onions/rust_quant
RUN_OKX_SIMULATED_E2E=1 OKX_REQUEST_EXPIRATION_MS=300000 \
  cargo test -p rust-quant-services --test okx_simulated_order_flow -- --ignored --nocapture
```

### 需要的环境变量（模拟盘 APIKey）
- `OKX_SIMULATED_API_KEY`
- `OKX_SIMULATED_API_SECRET`
- `OKX_SIMULATED_PASSPHRASE`

### 可调参数
- `OKX_TEST_INST_ID`（默认 `ETH-USDT-SWAP`）
- `OKX_TEST_SIDE`（默认 `buy`，可选 `sell`）
- `OKX_TEST_ORDER_SIZE`（默认 `1`）
- `OKX_TEST_TP_PCT` / `OKX_TEST_SL_PCT`（默认 `0.10`，测试用远离触发价，避免立即成交）

---

## 迭代标准

### 目标
- `win_rate >= 0.55` 且 `profit > 0`
- 优先更高 profit/Sharpe，兼顾回撤

### 判优/回退
- 记录最佳 back_test_log id 与对应 value/risk_config
- 若一次迭代胜率下降或盈利恶化，回退上一最佳参数
- 保持 JSON 合法（使用 JSON_OBJECT 避免转义问题）
- 可选 **Sharpe/回撤优先**：按 `Sharpe ↓ → MaxDD ↑ → Profit ↓` 排序，win_rate 作为参考

### 性能基准
| 配置 | win_rate | profit | 备注 |
|------|----------|--------|------|
| 原始基线 | ~52% | +5.5 | 起点 |
| 组合E | 54.7% | +52.77 | 旧最优 |
| **第一性原理v1** | **55.1%** | **+99.68** | **当前最优** |
| 出场优化 | ~34% | -85 | 有害，禁用 |

---

## 分析工具
- `scripts/vegas-backtest-analysis/scripts/analyze_backtest_detail.py`：输出 Top indicators + anomalies
- `scripts/vegas-backtest-analysis/scripts/visualize_backtest_detail.py`：生成 `dist/vegas_backtest_detail_<id>.png`，包含 Summary/Indicator detail/Anomalies 面板

---

## 自动化脚本模板（Python）

> 用于批量扫描参数并自动回写最优配置（示意脚本，可按需裁剪）

```python
import json
import os
import re
import subprocess
import pymysql

DB_HOST = "localhost"
DB_PORT = 33306
DB_USER = "root"
DB_PASS = "example"
DB_NAME = "test"

SIZES = [6, 7, 8]
WEIGHTS = [0.5, 0.7, 0.9]
BASELINE_ID = 5552

def db_conn():
    return pymysql.connect(
        host=DB_HOST,
        port=DB_PORT,
        user=DB_USER,
        password=DB_PASS,
        database=DB_NAME,
        charset="utf8mb4",
        cursorclass=pymysql.cursors.DictCursor,
    )

def fetch_log(log_id):
    with db_conn() as conn:
        with conn.cursor() as cursor:
            cursor.execute(
                "SELECT id, win_rate, profit, final_fund, sharpe_ratio, max_drawdown "
                "FROM back_test_log WHERE id=%s",
                (log_id,),
            )
            return cursor.fetchone()

def update_config(size, weight):
    with db_conn() as conn:
        with conn.cursor() as cursor:
            cursor.execute("SELECT value FROM strategy_config WHERE id=11 FOR UPDATE")
            data = json.loads(cursor.fetchone()["value"])

            ms = data.get("market_structure_signal") or {}
            ms["enable_swing_signal"] = False
            ms.setdefault("enable_internal_signal", True)
            ms.setdefault("swing_threshold", 0.015)
            ms.setdefault("internal_threshold", 0.015)
            ms.setdefault("is_open", True)
            data["market_structure_signal"] = ms

            leg = data.get("leg_detection_signal") or {}
            leg["size"] = size
            leg["is_open"] = True
            data["leg_detection_signal"] = leg

            weights = data.get("signal_weights") or {}
            weight_list = weights.get("weights") or []
            updated = []
            found_leg = False
            found_ms = False
            for name, w in weight_list:
                if name == "LegDetection":
                    updated.append([name, weight])
                    found_leg = True
                elif name == "MarketStructure":
                    updated.append([name, 0.0])
                    found_ms = True
                else:
                    updated.append([name, w])
            if not found_leg:
                updated.append(["LegDetection", weight])
            if not found_ms:
                updated.append(["MarketStructure", 0.0])
            weights["weights"] = updated
            data["signal_weights"] = weights

            cursor.execute(
                "UPDATE strategy_config SET value=%s WHERE id=11",
                (json.dumps(data, separators=(",", ":")),),
            )
            conn.commit()

def run_backtest():
    env = os.environ.copy()
    env["TIGHTEN_VEGAS_RISK"] = "0"
    env["DB_HOST"] = "mysql://root:example@localhost:33306/test?ssl-mode=DISABLED"
    env["CARGO_TERM_COLOR"] = "never"

    proc = subprocess.Popen(
        ["./target/release/rust_quant"],
        stdout=subprocess.PIPE,
        stderr=subprocess.STDOUT,
        text=True,
        bufsize=1,
        env=env,
        cwd="/Users/mac2/onions/rust_quant",
    )

    backtest_id = None
    for line in proc.stdout:
        if "回测日志保存成功" in line:
            match = re.search(r"back_test_id=(\\d+)", line)
            if match:
                backtest_id = int(match.group(1))
        if "全部回测执行成功" in line:
            break

    proc.terminate()
    try:
        proc.wait(timeout=10)
    except subprocess.TimeoutExpired:
        proc.kill()

    return backtest_id

baseline = fetch_log(BASELINE_ID)
best = baseline

for size in SIZES:
    for weight in WEIGHTS:
        update_config(size, weight)
        backtest_id = run_backtest()
        row = fetch_log(backtest_id)
        # 可自定义判优逻辑
        if row and row["profit"] > best["profit"]:
            best = row

print("Best:", best)
```

---

## 常用 SQL 查询例子

### 1) 查最新回测
```sql
SELECT id, win_rate, profit, final_fund, sharpe_ratio, max_drawdown, created_at
FROM back_test_log
WHERE strategy_type='Vegas'
ORDER BY id DESC
LIMIT 1;
```

### 2) 查最佳（Sharpe ↓ → MaxDD ↑ → Profit ↓）
```sql
SELECT id, win_rate, profit, final_fund, sharpe_ratio, max_drawdown, created_at
FROM back_test_log
WHERE strategy_type='Vegas' AND inst_type='ETH-USDT-SWAP' AND time='4H' AND profit > 0
ORDER BY sharpe_ratio DESC, max_drawdown ASC, profit DESC
LIMIT 5;
```

### 3) 查最近 N 条并对比指标
```sql
SELECT id, win_rate, profit, final_fund, sharpe_ratio, max_drawdown, created_at
FROM back_test_log
WHERE strategy_type='Vegas' AND inst_type='ETH-USDT-SWAP' AND time='4H'
ORDER BY id DESC
LIMIT 20;
```

### 4) 查某回测的明细统计
```sql
SELECT option_type, COUNT(*) AS cnt, SUM(CAST(profit_loss AS DECIMAL(16,4))) AS total_profit
FROM back_test_detail
WHERE back_test_id = 5552
GROUP BY option_type;
```

---

## 迭代日志

详细迭代记录见：`docs/VEGAS_ITERATION_LOG.md`

### 快速现状（2026-01-07）
- 基线（5576）：stop_loss 关，`max_loss_percent=0.05`，win_rate≈56.6%，profit≈1556.7，Sharpe≈1.44，dd≈52.6%。
- Sharpe优先（5570）：stop_loss 开，`max_loss_percent=0.05`，Sharpe≈1.63 但胜率≈45.8%（低于50%）。
- 0.055 止损或高波动硬过滤（5575/5577/5579）未超越基线；已回滚基线配置。

### 待验证方向（尚未实装）
1) 高波动自适应止损：布林宽/ATR 高时暂降 `max_loss_percent` 到 ~0.045，其余保持 0.05。
2) 连续亏损冷却：同向连续多次“最大亏损止损”后减仓或冷却，防连踩。
3) 温和长周期确认：要求日线 EMA 斜率与 4H 同向才放行（比全局硬过滤更宽松）。
4) 分层出场：1R/1.5R 先减半仓，余仓用反向信号平仓，兼顾胜率与尾部。
5) 极端波动过滤：极端K（>5%-8%涨跌）后一根内拒绝入场，降低爆量止损。

---

## 注意事项
- 环境变量默认从 .env 加载，但 DB_HOST 在命令行覆盖为本地 MySQL 容器
- `TIGHTEN_VEGAS_RISK=0` 禁用代码层强制风控收紧（推荐）
- 邮件发送可能报 warning，可忽略
- 频繁迭代时仅改 DB 配置，不改代码，可避免重编译
- MySQL 若使用 `caching_sha2_password`，PyMySQL 需安装 `cryptography`（可用 `env -u all_proxy -u ALL_PROXY -u no_proxy -u NO_PROXY ./.venv/bin/python -m pip install cryptography`）
- 使用 `JSON_OBJECT()` 函数构建 JSON 可避免引号转义问题
- **新模块开发**：先设权重=0验证，再调整权重

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fairwic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
