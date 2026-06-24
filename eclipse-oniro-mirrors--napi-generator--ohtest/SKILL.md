---
name: ohtest
description: OpenHarmony 测试辅助：ohtest.py 按 .d.ts 生成 ohosTest（四类边界）；uitest_gen.py 按 .ets 生成 UITest；fuzztest/find_fuzztest、find_actstest/actstest、coverage_analysis、coverage_gap_tests。详见各脚本与正文。 Use when this capability is needed.
metadata:
  author: eclipse-oniro-mirrors
---

# OpenHarmony 单元测试补全技能 (ohtest)

根据 **.d.ts 接口文件**（如 NAPI 生成的 `Index.d.ts`）在 **ohosTest 的 test 文件夹**下自动补全单元测试套件，参照 `Ability.test.ets` 结构，以接口为测试对象，生成符合 Hypium 规范的测试用例。

## 应用示例与提示词

在 **napi_generator 仓库根** 或工程目录下执行；脚本均在 **`src/skills/ohtest/`**。

| 场景 | 命令示例 | 提示词示例 |
|------|----------|------------|
| d.ts 生成单测 | `python3 src/skills/ohtest/ohtest.py --dts entry/.../Index.d.ts --test-dir entry/src/ohosTest/ets/test` | 「根据 Index.d.ts 生成 ohosTest 套件并注册入口」 |
| UITest | `python3 src/skills/ohtest/uitest_gen.py --ets entry/src/main/ets/pages/Index.ets ...` | 「给 Index.ets 生成 UITest」 |
| 跑 fuzz | `python3 src/skills/ohtest/fuzztest.py run -ts GetAppStatsMahFuzzTest -p rk3568` | 「编译并跑这个 fuzz 目标」 |
| 扫 fuzz 套件 | `python3 src/skills/ohtest/find_fuzztest.py` | 「仓库里有哪些 fuzztest」 |
| ACTS | `python3 src/skills/ohtest/actstest.py run <SuiteName>` | 「在 out 里跑指定 ACTS suite」 |
| 覆盖率 | `python3 src/skills/ohtest/coverage_analysis.py run -t <部件> -p rk3568` | 「拉覆盖率并分析」 |

## 功能说明

- **输入**：接口定义文件（如 `entry/src/main/cpp/types/libentry/Index.d.ts`）、测试目录（如 `entry/src/ohosTest/ets/test`）、可选模块导入名（如 `libentry.so`）。
- **输出**：在 test 目录下新增一个 `*Test.test.ets` 文件，并在现有 `List.test.ets`（或主测试入口）中注册该测试套。
- **命名规则**：接口文件名去掉特殊符号 + `Test`。例如 `Index.d.ts` → `IndexdtsTest`；函数名为 `indexdtsTest()`，describe 套件名为 `IndexdtsTest`。
- **测试套结构**：与 `Ability.test.ets` 一致：
  - `export default function <name>Test() {`
  - `describe('<Name>Test', () => { ... })`
  - 默认包含 `beforeAll`、`beforeEach`、`afterEach`、`afterAll`。
  - 每个接口方法对应多组 `it('<method>_tc_N', 0, () => { ... })`。

## 四类边界测试用例

对每个 .d.ts 中的导出接口方法，生成 4 个用例：

| 类型     | 说明     | 示例（以 `add(a: number, b: number) => number` 为例） |
|----------|----------|--------------------------------------------------------|
| 正常值   | 各种允许的输入类型、典型值 | `add(1, 2)`，`expect(result).assertEqual(3)` |
| 最大值   | 输入类型的最大值         | `add(Number.MAX_SAFE_INTEGER, 0)`，断言与预期一致 |
| 最小值   | 输入类型的最小值         | `add(Number.MIN_SAFE_INTEGER, 0)` 或负值边界 |
| 异常/压力 | 非数据类型、转换或大量调用 | 如 1000 次 `add(1, 1)`，每次 `expect(...).assertEqual(2)` |

用例内调用接口方法，并用 `expect(返回值).assertEqual(预期值)`（或 `assertContain` 等）做断言。

## 代码结构与编码规范

生成测试用例时需遵守以下约定，本技能在生成代码时已落实基础实现。

### 代码结构

- **公共常量**：测试中使用的数值、字符串等常量应集中在 `constant.ets` 中定义并导出，测试文件通过 `import { ... } from './constant'` 引用。生成器在首次生成前会检查 test 目录下是否存在 `constant.ets`，若不存在则自动创建并写入最小常量集（如 `HILOG_DOMAIN`、`TEST_FILTER`、`VAL_0`～`VAL_3`、`STRESS_1000`、`MAX`、`MIN` 等），后续可手动扩展。

### 编码规范

1. **行宽**：单行不超过 120 字符；过长注释或表达式应换行。
2. **用例间隔**：每个 `it()` 用例块之间保留一个空行，便于阅读与 diff。
3. **魔数**：代码中不直接写魔数（如 `0x0000`、`0`、`1000`），改用 `constant.ets` 中的常量名（如 `HILOG_DOMAIN`、`TEST_FILTER`、`STRESS_1000`）。
4. **文件拆分**：单个测试文件若超过 2000 行，应拆分为多个文件（如 `Indexdts_test_1.ets`、`Indexdts_test_2.ets`），并在主入口中按序引入；生成器目前不自动拆分，需人工处理超大文件。

生成内容已做到：使用 `constant.ets` 与导入常量、`it()` 间空行、hilog/expect 使用常量名、注释控制行宽；更多常量或拆分逻辑可在生成后手动补充。

## UITest（页面 UI 测试）

根据 **.ets 页面文件**（如 `pages/Index.ets`）在 **ohosTest 的 test 目录**下生成 **UI 测试套件**，实现对页面的单元级 UI 测试。参考 [HarmonyOS UITest 指南](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/uitest-guidelines) 与 [arkXtest User Guide](https://www.seaxiang.com/blog/093bade094ba4c79bb42016a9e1efadc)。

### 功能说明

- **输入**：页面 .ets 文件路径（如 `entry/src/main/ets/pages/Index.ets`）、测试目录（如 `entry/src/ohosTest/ets/test`）、可选 Ability 名称（默认 `EntryAbility`）。
- **输出**：在 test 目录下新增 `<StructName>Ui.test.ets`（如 `IndexUi.test.ets`），并在 `List.test.ets` 中注册该测试套。
- **解析内容**：从页面中解析 `struct` 名、`@State` 初始文本、`Text(this.xxx)` / `Text('literal')`、`.onClick` 内 `this.xxx = 'yyy'`、以及 `Row()` / `Column()` 布局。
- **生成用例**：
  - **页面加载**：断言当前 Top Ability 为指定 Ability。
  - **布局**：断言存在 `Row` / `Column`（`ON.type('Row')` / `ON.type('Column')`）。
  - **控件**：对每个初始显示的文本断言存在（`ON.text('...')`、`assertComponentExist`）。
  - **动作**：对每个带 `onClick` 且会改变 `@State` 的控件，生成「findComponent → click → assertComponentExist(变化后文本)」用例。
- **框架**：使用 `@kit.TestKit` 的 `Driver`、`ON`、`abilityDelegatorRegistry`，以及 `@ohos/hypium` 的 `describe` / `it` / `expect`；常量从 `constant.ets` 引入（`TEST_FILTER`、`UI_DELAY_MS`）。

### 何时使用

- 用户说：「对 Index.ets 实现 UI 测试」「为页面生成 UITest」「根据页面控件和布局写 UI 单元测试」。
- 需要对 ArkUI 页面做自动化 UI 测试：页面加载、布局存在、控件存在、点击后状态/文案变化。

### 使用方式

```bash
# 必选：页面 .ets 文件、测试目录；可选：Ability 名、是否更新 List.test.ets
python3 src/skills/ohtest/uitest_gen.py \
  --ets /path/to/entry/src/main/ets/pages/Index.ets \
  --test-dir /path/to/entry/src/ohosTest/ets/test \
  [--ability-name EntryAbility] \
  [--no-update-list]
```

生成文件命名：`<StructName>Ui.test.ets`（如 `IndexUi.test.ets`），套件名为 `<StructName>UiTest`（如 `IndexUiTest`）。若 test 目录下已有 `constant.ets`，生成器会追加 `UI_DELAY_MS`（若缺失），与 dts 单元测试共用同一 constant 文件。

### 参照与限制

- **参照**：[HarmonyOS UITest 指南](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/uitest-guidelines)、[arkXtest UiTest](https://www.seaxiang.com/blog/093bade094ba4c79bb42016a9e1efadc)（Driver.create、findComponent(ON.text)、click、assertComponentExist）。
- **限制**：解析基于简单正则，仅识别 `@State` 字符串、`Text(this.xxx)` / `Text('literal')`、`.onClick` 内 `this.xxx = 'yyy'` 及 `Row()` / `Column()`；复杂表达式或动态文本需生成后人工补充用例。

---

## 何时使用（dts 单元测试）

- 用户说：「根据 Index.d.ts 补全/生成单元测试」「为 libentry 接口写测试」「在 ohtest 里增加以 .d.ts 为对象的测试套」。
- 需要以 NAPI/TS 接口为对象，在 ohosTest 下快速生成符合规范的边界测试用例时。

## 使用方式（dts 单元测试）

```bash
# 必选：接口定义文件、测试目录；可选：模块名（默认 libentry.so）、是否更新 List.test.ets
python3 src/skills/ohtest/ohtest.py \
  --dts /path/to/entry/src/main/cpp/types/libentry/Index.d.ts \
  --test-dir /path/to/entry/src/ohosTest/ets/test \
  [--module libentry.so] \
  [--no-update-list]
```

生成文件命名：`<基名>.test.ets`，基名为接口文件名去掉特殊符号（如 `Index.d.ts` → `Indexdts`），故得 `Indexdts.test.ets`；套件名为基名+`Test`（如 `IndexdtsTest`），describe 与 export default function 分别为 `IndexdtsTest`、`indexdtsTest`。

## 参照模板

- 结构参照：`Ability.test.ets`（`export default function abilityTest()`、`describe('ActsAbilityTest', () => { ... })`、beforeAll/beforeEach/afterEach/afterAll、`it('assertContain', 0, () => { ... expect(...).assertEqual(...) })`）。
- 测试对象：来自 `Index.d.ts` 的导出接口（如 `add`），导入方式与工程一致（如 `import lib from 'libentry.so'`，调用 `lib.add(...)`）。

---

## Fuzz 测试执行（fuzztest.py）

在正确的工作目录和环境（含 hdc 路径）下调用 developer_test 的 `start.sh` 执行 FUZZ 测试套。

### 环境与路径

- **工作目录**：执行时在 `test/testfwk/developer_test` 下调用 `./start.sh`。
- **hdc**：框架通过 `shutil.which("hdc")` 查找 hdc。脚本会将 **`${OHOS_SDK_PATH}/linux/toolchains`**（及 `toolchains/bin` 若存在）加入 `PATH`，以便框架找到 hdc；未设置 `OHOS_SDK_PATH` 时需保证系统 `PATH` 中已有 hdc。
- **DEVTESTDIR**：脚本会设置 `DEVTESTDIR` 为 developer_test 的绝对路径，与框架约定一致。

### 何时使用

- 用户说：「执行 GetAppStatsMahFuzzTest 的 fuzz 测试」「跑 FUZZ 用例」「执行 fuzztest」。
- 需要在本机通过 developer_test 框架在设备上跑指定 FUZZ 测试套时。

### 使用方式

```bash
# 方式一：按测试套名执行（-ts 与 -ss/-tp 至少填其一）
python3 src/skills/ohtest/fuzztest.py run -ts GetAppStatsMahFuzzTest
python3 src/skills/ohtest/fuzztest.py run -ts GetAppStatsMahFuzzTest -p rk3568
python3 src/skills/ohtest/fuzztest.py run -ts GetAppStatsMahFuzzTest --dry-run

# 方式二：按子系统、部件执行（等价于 start.sh -p 3568 run -t FUZZ -ss customization -tp customization）
python3 src/skills/ohtest/fuzztest.py run -ss customization -tp customization -p 3568
python3 src/skills/ohtest/fuzztest.py run -ss customization -tp customization --dry-run

# 需收集覆盖率时再加 --coverage
python3 src/skills/ohtest/fuzztest.py run -ts GetAppStatsMahFuzzTest --coverage

python3 src/skills/ohtest/fuzztest.py help
```

| 参数 | 说明 |
|------|------|
| `-ts` / `--testsuite` | 测试套名，如 `GetAppStatsMahFuzzTest`；与 `-ss`/`-tp` 至少填其一 |
| `-ss` / `--subsystem` | 子系统，如 `customization`，与 start.sh 的 `run -t FUZZ -ss` 一致 |
| `-tp` / `--testpart` | 部件，如 `customization`，与 start.sh 的 `run -t FUZZ -tp` 一致 |
| `-p` / `--product` | 产品名，默认 `rk3568`（如 3568 则传 `-p 3568`） |
| `--coverage` | 可选：附加 `-cov coverage` 收集覆盖率（拉取与分析时需设备上有 gcda） |
| `--dry-run` | 仅打印将要执行的命令，不实际执行 |

执行前需：设备已连接、hdc 可用（设置 `OHOS_SDK_PATH` 或系统 PATH 中含 hdc）。

---

## 查找工程内所有 Fuzztest（find_fuzztest.py）

从工程目录扫描含 **bundle.json** 的部件（有 fuzztest 与无 fuzztest 的均列入同一张表）；有 fuzztest 的解析 **BUILD.gn** 得到测试套件与 **\*_feature_coverage** 覆盖率选项，无 fuzztest 的对应列填 **无**。表格增加一列：部件从 **src** 起的**相对路径**。默认输出 **src/partwithfuzztest.md**。

### 使用方式

```bash
# 默认扫描整个 src（含 base、arkcompiler、developtools、device 等），输出到 src/partwithfuzztest.md
python3 src/skills/ohtest/find_fuzztest.py

# 仅扫描 base 目录
python3 src/skills/ohtest/find_fuzztest.py --root base

# 指定输出文件
python3 src/skills/ohtest/find_fuzztest.py -o /path/to/partwithfuzztest.md
```

| 参数 | 说明 |
|------|------|
| `--root` | 相对 src 的扫描根目录，默认 `.`（整个 src）；仅 base 时传 `base` |
| `--output` / `-o` | 输出 Markdown 文件路径，默认 **src/partwithfuzztest.md** |

表格列：**子系统 | 部件 | 相对路径（从 src 起） | 覆盖率编译选项 | Fuzztest 测试套件**。无 fuzztest 的部件在覆盖率与测试套件列填「无」。

---

## 查找所有 ACTS 测试套件（find_actstest.py）

在 **test/xts/acts** 下扫描所有 **BUILD.gn**，识别 **ohos_*_suite**（如 ohos_app_assist_suite、ohos_js_app_suite、ohos_moduletest_suite）定义的 ACTS 测试套件，解析 **hap_name**、**subsystem_name**、**part_name** 及编译对象类型；统计子系统数、部件数、ACTS 测试套件数、目录数，并输出明细表到 **src/all_acts.md**。编译入口来自 bundle.json 的 test 节点：`//test/xts/acts/build:acts_group`。

### 使用方式

```bash
# 默认输出 src/all_acts.md
python3 src/skills/ohtest/find_actstest.py

# 指定输出文件
python3 src/skills/ohtest/find_actstest.py -o /path/to/all_acts.md
```

输出表格列：**目录 | 子系统 | 部件 | 测试套件名 | hap_name | 编译对象**。

---

## 分析 fuzztest 测试覆盖率（coverage_analysis.py）

对设备上已有的 gcda 收集、生成 .gcov 并统计覆盖率（设备上需曾跑过带 `-cov coverage` 的 fuzz 测试才会产生 gcda）。

### 流程

1. **在设备上跑 fuzz 测试**（若需收集覆盖率，运行时加 `--coverage`）  
   `python3 src/skills/ohtest/fuzztest.py run -ts GetAppStatsMahFuzzTest`  
   或按子系统/部件：`python3 src/skills/ohtest/fuzztest.py run -ss customization -tp customization`  
   需覆盖率时：`python3 src/skills/ohtest/fuzztest.py run -ts GetAppStatsMahFuzzTest --coverage`
2. **收集覆盖率并生成报告**  
   - 按测试对象拉取（推荐）：`python3 src/skills/ohtest/coverage_analysis.py run -t customization -p rk3568`  
     报告目录为 **reports/obj_customization_yymmddhhmmss**，且仅拉取设备路径中包含 `customization` 的 gcda。  
   - 拉取全部：`python3 src/skills/ohtest/coverage_analysis.py run [-p rk3568]`  
     报告目录默认 reports/obj。  
   会从设备上两个路径查找 *.gcda，拷贝到报告目录后，再拷贝对应 *.gcno 与源码，在 test/testfwk 下执行 gcov，并将 .gcov 移入报告目录。
3. **查看覆盖率统计并生成 analysis.md**  
   `python3 src/skills/ohtest/coverage_analysis.py analyze [目录]`  
   解析该目录下的 .gcov，输出可执行行、已覆盖行、覆盖率；并**在该目录下写入 analysis.md**，其中对覆盖率不足 100% 的文件列出**未覆盖代码行（行号与内容）**及**测试建议**（分支/错误处理/空指针等）。

### 环境

- **hdc**：与 fuzztest 相同，脚本会将 **`${OHOS_SDK_PATH}/linux/toolchains`**（及 `toolchains/bin`）加入 PATH；未设置时需保证系统 PATH 中已有 hdc。
- 执行 `run` 拉取前，设备上需已有 gcda（即曾跑过带 `-cov coverage` 的 fuzz 测试）。

### 技能 1：清除分析结果并再次分析

清除 reports/obj 下的覆盖率相关文件（*.gcda、*.gcno、*.cpp、*.gcov），再从设备拉取 gcda、拷贝 gcno/cpp、执行 gcov，最后执行 analyze 输出统计。

```bash
python3 src/skills/ohtest/coverage_analysis.py clear-analyze
python3 src/skills/ohtest/coverage_analysis.py clear-analyze -p rk3568
```

### 技能 2：清除分析结果、重新运行 fuzztest 并分析

先清除 reports/obj；再在设备上执行 fuzz 测试（调用 fuzztest.py）；然后从设备拉取 gcda、生成 .gcov；最后执行 analyze 输出统计。

```bash
python3 src/skills/ohtest/coverage_analysis.py clear-rerun-fuzz-analyze
python3 src/skills/ohtest/coverage_analysis.py clear-rerun-fuzz-analyze -ts GetAppStatsMahFuzzTest -p rk3568
```

| 参数 | 说明 |
|------|------|
| `-ts` / `--testsuite` | fuzz 测试套名，默认 `GetAppStatsMahFuzzTest` |
| `-p` / `--product` | 产品名，默认 `rk3568` |
| `--device` | 指定设备 ID |
| `--search-root` | 设备上查找 *.gcda 的目录，可多次指定 |

### run 子命令：测试对象与目录命名

| 参数 | 说明 |
|------|------|
| `-t` / `--target` | 测试对象名（如 `customization`）。指定后：① 报告目录为 **reports/obj_<target>_yymmddhhmmss**；② 仅拉取设备路径中包含该名的 gcda。 |
| `--output-dir` | 手动指定报告目录；与 `-t` 同时指定时以本参数为准。 |

### analyze 输出：analysis.md

analyze 会在指定目录下生成 **analysis.md**，内容包括：一、覆盖率汇总表；二、对覆盖率 &lt;100% 的文件：**未覆盖代码行**（行号 + 代码内容）、**测试建议**（分支/错误处理/空指针等）。

---

## 生成覆盖率缺失的测试用例建议（coverage_gap_tests.py）

根据 **.gcov 覆盖率分析文件** 结合对应 **fuzztest** 测试用例，生成「覆盖率缺失的测试用例」建议。输出包含：**一、覆盖率缺失摘要**；**二、现有 fuzztest 目标**；**三、建议新增/修改的测试用例**（按文件列出未覆盖行与涉及符号）；**四、新增/修改文件建议**；**五、构建与运行**；**六、预期对覆盖率的影响**。参考此前新增 fuzzer（如 `battery_stats_info_fuzzer`）的流程：新增 fuzzer 目录与 BUILD.gn、在 group 的 deps 中注册、反序列化/API/分支类未覆盖时的 fuzzer 写法要点。

### 何时使用

- 用户说：「根据 gcov 分析缺哪些测试用例」「覆盖率缺失要加什么 fuzz 用例」「分析 battery_statistics 的覆盖率并给出补测建议」。
- 已有 `developer_test/reports/obj_<模块>_<id>` 下的 .gcov 报告，需要针对未覆盖行给出**新增 fuzzer、修改文件、构建运行与预期影响**的结构化建议时。

### 使用方式

```bash
# 指定报告目录（通常为 reports/obj_<模块>_<id>）；可选 --module、--output
python3 src/skills/ohtest/coverage_gap_tests.py analyze-gaps test/testfwk/developer_test/reports/obj_battery_statistics_2602051604
python3 src/skills/ohtest/coverage_gap_tests.py analyze-gaps test/testfwk/developer_test/reports/obj_battery_statistics_2602051604 --module battery_statistics
python3 src/skills/ohtest/coverage_gap_tests.py analyze-gaps test/testfwk/developer_test/reports/obj_battery_statistics_2602051604 -o coverage_gap_report.txt
# 不传报告目录时，若 reports 下仅有一个 obj_* 子目录则自动使用该目录
python3 src/skills/ohtest/coverage_gap_tests.py analyze-gaps
python3 src/skills/ohtest/coverage_gap_tests.py help
```

| 参数 | 说明 |
|------|------|
| `report_dir` | 报告目录，如 `developer_test/reports/obj_battery_statistics_2602051604`；可省略并由脚本自动查找 |
| `--module` / `-m` | 模块名（如 `battery_statistics`），用于解析现有 fuzztest 目标与模块路径 |
| `--output` / `-o` | 将报告写入该文件；不指定则打印到 stdout |

### 输出说明

- **一、覆盖率缺失摘要**：按 .gcov 文件列出未覆盖可执行行数及合计。
- **二、现有 fuzztest 目标**：从该模块的 `test/fuzztest/BUILD.gn` 的 deps 解析出的 FuzzTest 目标列表。
- **三、建议新增/修改的测试用例**：对未覆盖行较多的文件，列出涉及符号（如 `BatteryStatsInfo::Unmarshalling`）及示例行号与代码片段。
- **四、新增/修改文件建议**：新增 fuzzer 目录结构（`<source_stem>_fuzzer/`、`*_fuzzer_test.cpp`、BUILD.gn、project.xml、corpus/init）；在 group 的 deps 中注册；反序列化/Setter·Getter·分支类未覆盖时的写法要点。
- **五、构建与运行**：模块路径、`./build.sh --build-target <NewFuzzTest> --gn-args <module>_feature_coverage=true`、ohbuild 与 fuzztest/coverage_analysis 命令示例。
- **六、预期对覆盖率的影响**：按文件说明补充对应调用后可提高的行覆盖率。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eclipse-oniro-mirrors) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
