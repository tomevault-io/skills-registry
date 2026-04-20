---
name: ohbuild
description: OpenHarmony build skill: fuzz tests (list/build/verify-coverage gn-args), component fuzztest, and ACTS suite build via build-acts (test/xts/acts/build.sh). Use when users need fuzz or ACTS compilation from OH src. Use when this capability is needed.
metadata:
  author: eclipse-oniro-mirrors
---

# OH Build (ohbuild) 技能

用于 OpenHarmony 构建相关操作：编译 **fuzz** 测试、查看模块 fuzztest 及覆盖率 **gn-args**、以及 **`build-acts`** 在 **`test/xts/acts`** 下编指定 **ACTS suite**（与 **ohtest/actstest.py**「跑 suite」互补：此处侧重 acts 目录内 **`build.sh`** 编译链）。

## 应用示例与提示词

在 **OpenHarmony 源码根**（含 **`build.sh`**）下执行；脚本使用 **napi_generator** 内路径。

| 场景 | 命令示例 | 提示词示例 |
|------|----------|------------|
| 列 fuzz 目标 | `python3 <napi_generator>/src/skills/ohbuild/ohbuild.py list-fuzztest battery_manager` | 「列出 battery_manager 部件有哪些 fuzztest 目标」 |
| 编单个 fuzz | `python3 <napi_generator>/src/skills/ohbuild/ohbuild.py build-fuzztest GetAppStatsMahFuzzTest --gn-args battery_statistics_feature_coverage=true` | 「编译 GetAppStatsMahFuzzTest 并打开覆盖率 gn-args」 |
| 部件全 fuzz | `python3 <napi_generator>/src/skills/ohbuild/ohbuild.py build-component-fuzztest battery_statistics` | 「把该部件所有 fuzztest 编出来」 |
| 查覆盖率参数 | `python3 <napi_generator>/src/skills/ohbuild/ohbuild.py verify-coverage power_manager` | 「power_manager 开覆盖率要配什么 gn-args」 |
| 编 ACTS suite | `python3 <napi_generator>/src/skills/ohbuild/ohbuild.py build-acts ActsAACommandPrintSyncTest --src-dir ~/ohos/61release/src` | 「在 acts 目录编某个 ACTS 套件」「多个 suite 用逗号」 |
| 帮助 | `python3 <napi_generator>/src/skills/ohbuild/ohbuild.py help` | 「ohbuild 子命令怎么用」 |

### `build-acts`（ACTS 编译）

在 OpenHarmony **`src` 根** 下应存在 **`test/xts/acts/build.sh`**。脚本在该目录执行：

`./build.sh suite=acts system_size=<size> product_name=<product> suite=<suite1>,<suite2>,...`

| 参数 | 说明 |
|------|------|
| 第一个位置参数 | suite 名；多个用 **英文逗号** 分隔 |
| `--src-dir` | 工程 **`src` 根**（未传则用脚本内置推断的 `SRC_ROOT`，与 napi_generator 相对布局有关；**大仓开发建议总是传 `--src-dir`**） |
| `--product-name` | 默认 `rk3568` |
| `--system-size` | 默认 `standard` |
| `--no-run` | 只打印将执行的命令，不真正执行 |

---

## 技能一：编译 Fuzz 测试

### 命令格式

在源码根目录（含 `build.sh` 的目录，一般为 `src` 或工程根）执行：

```bash
./build.sh --build-target <编译目标名> --product-name rk3568 --gn-args <模块>_feature_coverage=true
```

### 参数说明

| 参数 | 含义 | 示例 / 默认 |
|------|------|-------------|
| `--build-target` | 要编译的 fuzz 测试目标名 | 如 `GetAppStatsMahFuzzTest` |
| `--product-name` | 产品名 | 默认 `rk3568` |
| `--gn-args` | GN 参数，用于开启该模块的覆盖率 | 如 `battery_statistics_feature_coverage=true` |

### 如何找到「编译目标名」

1. 确认模块下是否有 fuzz 测试：存在目录 **`test/fuzztest`** 即表示该模块有 fuzztest。
2. 打开 **`test/fuzztest/BUILD.gn`**。
3. 在 `group("fuzztest")` 的 `deps += [ ... ]` 中，每一项格式为 `"子目标路径:目标名"`，其中的 **目标名** 即为 `--build-target` 的取值。

示例（`src/base/powermgr/battery_statistics/test/fuzztest/BUILD.gn`）：

```gn
deps += [
  "getappstatsmah_fuzzer:GetAppStatsMahFuzzTest",
  "getappstatspercent_fuzzer:GetAppStatsPercentFuzzTest",
  ...
]
```

则 `--build-target` 可填：`GetAppStatsMahFuzzTest`、`GetAppStatsPercentFuzzTest` 等。

### 部件 fuzztest 编译目标（一次编译该部件下全部 fuzz 用例）

若要**一次编译该部件下所有 fuzz 测试**，使用「部件 fuzztest 编译目标」：

- 在部件目录下找到 **`test/fuzztest/BUILD.gn`**。
- 该文件中的 **`group("...")`** 名称（如 `group("fuzztest")` 中的 `fuzztest`）即为目标名。
- 编译目标格式：**`<模块相对 src 的路径>/test/fuzztest:<group 名>`**。

示例：`battery_statistics` 的 BUILD.gn 为 `group("fuzztest")`，模块路径为 `base/powermgr/battery_statistics`，则部件 fuzztest 编译目标为：

```text
base/powermgr/battery_statistics/test/fuzztest:fuzztest
```

编译命令示例：

```bash
./build.sh --build-target base/powermgr/battery_statistics/test/fuzztest:fuzztest --product-name rk3568 --gn-args battery_statistics_feature_coverage=true
```

使用 `ohbuild.py build-component-fuzztest <模块>` 可自动解析并打印上述命令（见下方「使用 Python 脚本」）。

### 无 test/fuzztest 目录时：从 bundle.json 取 fuzztest 目标

若部件下**没有** `test/fuzztest/BUILD.gn`，fuzztest 编译目标可能写在 **bundle.json** 的 **build.test** 中（如 `component.build.test`），格式为 `"//路径:目标名"`。编译时**去掉前缀 `//`** 作为 `--build-target`。

示例（`base/accesscontrol/sandbox_manager/bundle.json`）：

```json
"build": { "test": ["//base/accesscontrol/sandbox_manager:sandbox_manager_build_fuzz_test", ...] }
```

则 `--build-target` 为：`base/accesscontrol/sandbox_manager:sandbox_manager_build_fuzz_test`。

此类部件的 **`*_feature_coverage`** 常在 **`config/BUILD.gn`** 的 `declare_args()` 中定义，ohbuild 会一并解析。

### 如何找到「gn-args 参数」

1. 打开该模块的 **BUILD.gn**（常见位置：**`config/BUILD.gn`**、`utils/BUILD.gn` 或根目录 BUILD.gn，如 `src/base/accesscontrol/sandbox_manager/config/BUILD.gn`）。
2. 在 `declare_args() { ... }` 中查找名为 **`<模块名>_feature_coverage`** 的变量（默认一般为 `false`）。
3. 编译 fuzz 并开覆盖率时，在命令行中写：`<模块名>_feature_coverage=true`。

示例（`battery_statistics/utils/BUILD.gn`）：

```gn
declare_args() {
  battery_statistics_feature_coverage = false
}
```

则对应 `--gn-args` 为：`battery_statistics_feature_coverage=true`。

### 命名规则

- 覆盖率开关变量：**`<模块名>_feature_coverage`**（如 `battery_statistics_feature_coverage`、`battery_manager_feature_coverage`）。

### 完整示例

编译 battery_statistics 的 GetAppStatsMahFuzzTest，并开启该模块覆盖率：

```bash
./build.sh --build-target GetAppStatsMahFuzzTest --product-name rk3568 --gn-args battery_statistics_feature_coverage=true
```

### 编译完毕后的验证环节（gcno 文件）

编译带覆盖率的 fuzz 测试后，应验证是否生成了模块相关的 **gcno** 文件（覆盖率信息文件）。在源码根目录（含 `build.sh` 的目录，一般为 `src`）下执行：

```bash
find out/rk3568/obj/ -name "*.gcno"
```

- **若有**与模块相关的 gcno 文件（路径中包含模块名，如 `power_manager`、`battery_statistics`），则说明该模块已开启覆盖率编译，可进行覆盖率收集与统计；建议将找到的 gcno 路径列出来提示用户。
- **若无**，请确认编译时使用了 `--gn-args <模块名>_feature_coverage=true`。

使用 ohbuild 脚本验证（可选指定模块名，只列出与该模块相关的 gcno）：

```bash
# 列出与 power_manager 相关的 gcno
python3 src/skills/ohbuild/ohbuild.py verify-coverage power_manager

# 列出 out/rk3568/obj 下所有 gcno
python3 src/skills/ohbuild/ohbuild.py verify-coverage
```

---

## 技能二：查看模块的 Fuzztest 及对应参数

用于回答「某模块有哪些 fuzz 测试、编译时要传什么参数」。

### 判断模块是否有 fuzztest

- 看该模块目录下是否存在 **`test/fuzztest`** 目录。
- 有则说明该模块提供 fuzz 测试。

### 查看所有支持的 fuzz 测试目标

1. 打开 **`<模块路径>/test/fuzztest/BUILD.gn`**。
2. 在 `group("fuzztest")` 的 `deps += [ ... ]` 中列出所有 `"xxx:目标名"`，**目标名** 即为可用的 `--build-target`。

示例：`battery_statistics` 的 fuzztest 包括 GetAppStatsMahFuzzTest、GetAppStatsPercentFuzzTest、GetBatteryStatsFuzzTest 等（以 BUILD.gn 为准）。

### 查看对应的 gn-args 参数

1. 在该模块的 **BUILD.gn** 中查找（常见位置：模块下的 `utils/BUILD.gn` 或主 BUILD.gn）。
2. 在 **`declare_args() { ... }`** 里找 **`<模块名>_feature_coverage`** 变量。
3. 编译该模块任一 fuzz 目标并开覆盖率时，使用：  
   `--gn-args <模块名>_feature_coverage=true`。

### 小结

- **Fuzz 目标列表**：来自 `test/fuzztest/BUILD.gn` 的 `deps` 中的目标名。
- **覆盖率参数**：来自模块 BUILD.gn 的 `declare_args()` 中 `*_feature_coverage`，命名规则为 **`<模块名>_feature_coverage`**，默认一般为 `false`。

---

## 使用 Python 脚本

可通过 `ohbuild.py` 查询模块 fuzztest 或生成编译命令：

```bash
# 查看某模块的 fuzz 测试目标与覆盖率参数（模块名或相对 src 的路径）
python3 src/skills/ohbuild/ohbuild.py list-fuzztest battery_manager
python3 src/skills/ohbuild/ohbuild.py list-fuzztest base/powermgr/battery_statistics

# 生成编译 fuzz 测试的命令（不执行，仅打印）
python3 src/skills/ohbuild/ohbuild.py build-fuzztest GetAppStatsMahFuzzTest --gn-args battery_statistics_feature_coverage=true

# 生成「编译部件全部 fuzztest」的命令（从 test/fuzztest/BUILD.gn 的 group 解析目标）
python3 src/skills/ohbuild/ohbuild.py build-component-fuzztest battery_statistics --gn-args battery_statistics_feature_coverage=true
python3 src/skills/ohbuild/ohbuild.py build-component-fuzztest base/powermgr/battery_statistics

# 编译后验证：是否有模块相关 gcno 文件（可选模块名）
python3 src/skills/ohbuild/ohbuild.py verify-coverage power_manager
python3 src/skills/ohbuild/ohbuild.py verify-coverage

# 编译 ACTS（在 test/xts/acts 下执行 build.sh；多 suite 逗号分隔）
python3 src/skills/ohbuild/ohbuild.py build-acts ActsAACommandPrintSyncTest --src-dir ~/ohos/61release/src

# 帮助
python3 src/skills/ohbuild/ohbuild.py help
```

**命令说明：**

- `build-acts <suite>[,suite2] [--src-dir PATH] [--product-name rk3568] [--system-size standard] [--no-run]`：在 **`test/xts/acts`** 目录执行 ACTS 编译；**`--no-run`** 时只打印将执行的命令。
- `list-fuzztest <模块>`：列出该模块下「部件 fuzztest 编译目标」、所有单个 fuzz 目标名及对应的 `*_feature_coverage` 参数。
- `build-fuzztest <目标名> [--product-name rk3568] [--gn-args xxx=true]`：打印编译**单个** fuzz 目标的 `./build.sh` 命令。
- `build-component-fuzztest <模块> [--product-name rk3568] [--gn-args xxx=true]`：打印编译**部件全部 fuzztest** 的 `./build.sh` 命令；目标从该部件 `test/fuzztest/BUILD.gn` 的 `group("...")` 解析，形如 `base/powermgr/battery_statistics/test/fuzztest:fuzztest`；若不传 `--gn-args` 则自动用该模块的 `*_feature_coverage=true`。
- `verify-coverage [模块名] [--product-name rk3568]`：编译后验证，在 `out/<product>/obj/` 下查找 `*.gcno`；若指定模块名则只列出与该模块相关的 gcno，若有则列出并提示用户。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eclipse-oniro-mirrors) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
