---
name: swift-private-bundle
description: Use when working with private Swift Package Manager dependencies from github.com/hocgin, especially when you need to discover, verify, or integrate a package and should first refresh and search the local ~/GitHub/knowledge mirror of github.com/hocgin/knowledge, then fall back to gh and the target repository when needed.
metadata:
  author: hocgin
---

在处理 `github.com/hocgin` 旗下的私有 Swift 依赖时，先把自己当成“带知识库检索能力的 SPM 集成助手”，而不是通用依赖搜索器。

## 触发条件

以下场景应使用本 skill：

- 用户要接入、排查、升级、替换 `github.com/hocgin/*` 的私有 Swift Package
- 用户给出了 `github.com/hocgin/...` 的仓库地址、包名、模块名或 import 名称
- 你在 `Package.swift`、`Package.resolved`、`.pbxproj`、README 中看到了 `hocgin` 私有仓库
- 用户想知道某个私有包怎么用、支持什么平台、应该依赖哪个版本

以下场景不要优先使用本 skill：

- 依赖不属于 `github.com/hocgin`
- 用户只是做通用 Swift / SPM 问题排查，且与 `hocgin` 私有仓库无关

## 核心规则

- 如果本地存在 `~/GitHub/knowledge/`，必须先尝试更新后再使用
- 优先检索本地 `~/GitHub/knowledge/`，再回退到 `gh` 检索 `github.com/hocgin/knowledge`
- 只有知识库信息不足时，才继续查看目标私有仓库
- 优先确认这是一个可用的 Swift Package，再讨论如何集成
- 优先使用稳定 tag 或明确版本，不默认使用 `main` / `master`
- 输出使用中文

## 标准流程

### 1. 先识别用户到底在找什么

先从用户输入或当前项目中提取这些线索：

- 仓库名
- Package 名
- target / product 名
- `import Xxx`
- 功能关键词，例如“网络层”“蓝牙”“埋点”“知识库”

如果项目已存在依赖配置，优先检查：

- `Package.swift`
- `Package.resolved`
- `*.xcodeproj/project.pbxproj`

### 2. 第一优先级：检索 `hocgin/knowledge`

先看本地是否已有 `~/GitHub/knowledge/`，如果有，先尝试更新；更新成功或失败都要根据实际结果继续，不要因为更新失败就直接放弃本地副本。

推荐顺序：

1. 本地存在 `~/GitHub/knowledge/` 时，先尝试更新
2. 使用本地知识库检索
3. 本地不存在，或本地检索不到，再用 `gh` 检索远端 `hocgin/knowledge`
4. 知识库仍然不足时，再查看目标私有仓库

优先检索的内容：

- 包名 / 仓库名
- 模块名 / import 名
- 功能关键词
- SPM 片段，例如 `.package(`、`product(name:`、`Package.swift`

可用命令示例：

```bash
# 中文注释：如果本地知识库存在，先尝试更新
git -C ~/GitHub/knowledge pull --ff-only

# 中文注释：本地优先检索知识库
rg -n "SomePackage|SomeModule|github.com/hocgin/some-repo|\\.package\\(|product\\(name:" ~/GitHub/knowledge

# 中文注释：本地没有命中时，再看远端知识库仓库是否可访问
gh repo view hocgin/knowledge

# 中文注释：按关键词在远端知识库代码中检索
gh search code "Package.swift repo:hocgin/knowledge hocgin"
gh search code "\"SomePackage\" repo:hocgin/knowledge"
gh search code "\"import SomeModule\" repo:hocgin/knowledge"
gh search code "\"github.com/hocgin/some-repo\" repo:hocgin/knowledge"

# 中文注释：如果知识库有文档目录，可继续按主题搜
gh search code "network repo:hocgin/knowledge"
gh search code "swift package repo:hocgin/knowledge"
```

如果知识库里命中了文档、示例、接入说明、版本约束或模块名映射，优先基于这些信息回答。

回答时要明确说明信息来源：

- 来自本地 `~/GitHub/knowledge/`
- 来自远端 `github.com/hocgin/knowledge`
- 或来自目标仓库结构推断

### 3. 第二优先级：查看目标仓库本身

只有当知识库缺少足够信息时，才查看目标仓库。

重点检查：

- 是否存在 `Package.swift`
- package / product / target 名称
- 支持的平台和 Swift tools version
- 最近可用 tag
- README 中的接入方式

可用命令示例：

```bash
# 中文注释：查看仓库基础信息
gh repo view hocgin/some-repo

# 中文注释：查看标签，优先选稳定版本
gh release list -R hocgin/some-repo
gh api repos/hocgin/some-repo/tags

# 中文注释：读取 Package.swift 内容确认可作为 SPM 使用
gh api repos/hocgin/some-repo/contents/Package.swift
```

如果需要进一步确认文件内容，可以在有权限时用 `gh repo clone` 到本地再检查。

### 4. 评估是否能接入当前项目

至少确认以下几点：

- 当前项目使用的是 SPM 还是 Xcode UI 添加包
- 依赖地址是否应该使用 HTTPS 或 SSH
- 目标 package 暴露了哪些 products
- 平台版本是否兼容当前项目
- 是否需要额外的私有依赖链

### 5. 给出可执行的集成方案

默认优先给 SPM 方案，并明确版本。

示例：

```swift
dependencies: [
  // 中文注释：优先使用明确 tag，避免直接跟踪不稳定分支
  .package(url: "git@github.com:hocgin/some-repo.git", exact: "1.2.3")
]
```

如果还需要声明 product，补充：

```swift
.target(
  name: "App",
  dependencies: [
    // 中文注释：这里要写 product 名，而不是仓库名
    .product(name: "SomeProduct", package: "some-repo")
  ]
)
```

## 回答时的输出顺序

按这个顺序组织答案：

1. 你识别到的包线索
2. 本地或远端 `hocgin/knowledge` 的命中结果
3. 若有必要，目标仓库的补充信息
4. 推荐使用的仓库 / product / 版本
5. 应如何修改 `Package.swift` 或 Xcode
6. 如何验证集成成功

## 验证检查

如果当前目录是 Swift 项目，可优先使用这些命令：

```bash
# 中文注释：解析依赖，确认私有仓库能被拉取
swift package resolve

# 中文注释：查看最终依赖图，确认目标包已进入图中
swift package show-dependencies

# 中文注释：必要时查看 manifest 解析结果
swift package dump-package
```

如果是 Xcode 工程，则说明需要在 Xcode 中重新 resolve package，或执行对应构建命令验证。

## 失败与回退策略

### `gh` 未登录或无权限

先检查：

```bash
gh auth status
```

然后明确告诉用户：当前无法访问 `hocgin/knowledge` 或目标私有仓库，因此不能把猜测当成结论。

### 本地知识库存在但更新失败

先说明更新失败，再决定是否继续使用当前本地副本：

- 如果本地副本仍可读，继续检索，但明确标注“本地副本未成功更新，结果可能不是最新”
- 如果本地副本损坏或不可读，再回退到 `gh` 检索远端知识库

### 本地知识库不存在，或知识库没有命中

可以继续：

- 用 `gh` 检索远端 `hocgin/knowledge`
- 直接查看目标 repo
- 在当前项目中搜索已有引用
- 根据 `Package.swift` / `Package.resolved` 推断真实包名和 product 名

但要明确说明“这是基于仓库结构推断，不是来自最新知识库文档”。

### 本地知识库是 git 仓库但工作区不干净

不要擅自丢弃本地改动。

可以这样处理：

- 先执行只读检查，例如 `git -C ~/GitHub/knowledge status --short`
- 如果工作区不干净，明确说明无法安全执行 `pull --ff-only`
- 在这种情况下，优先读取当前本地副本，并标注“本地知识库未更新”
- 如有必要，再回退到 `gh` 检索远端

### 仓库存在但不是标准 Swift Package

明确指出：

- 缺少 `Package.swift`
- 或没有可消费的 product
- 或平台 / tools version 不兼容

不要把这类仓库当成可直接 SPM 集成的候选项。

## 禁止事项

- 不要跳过本地 `~/GitHub/knowledge/` 与 `hocgin/knowledge` 直接开始猜
- 不要在本地知识库存在时，未尝试更新就把它当成最新来源
- 不要把仓库名直接当成 product 名
- 不要默认推荐开发分支
- 不要在没有权限验证的情况下声称“这个私有包可用”
- 不要泄露 token、私钥或其他敏感凭据

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hocgin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
