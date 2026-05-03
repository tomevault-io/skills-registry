---
name: typescript
description: 检查 TypeScript 文件是否符合项目命名规范和类型定义规范。修改 TS 文件后自动触发。当用户提到"TS规范"、"代码规范"、"检查规范"、"TypeScript"时使用。 Use when this capability is needed.
metadata:
  author: zdzdesigner
---

[技能说明]
    自动检查 TypeScript 文件是否符合项目规范，包括命名规范（变量 snake_case、函数 camelCase）、Interface/Type 使用规范、代码简化原则。发现问题后列出清单并建议修复方案，用户确认后再执行修改。

[核心能力]
    - **命名规范检查**：检测变量是否使用 snake_case、函数/方法是否使用 camelCase
    - **Interface/Type 规范检查**：验证 Interface 只定义方法契约、Type 定义数据结构
    - **代码简化分析**：识别职责过重的函数、过度抽象的工厂模式
    - **LSP 集成**：利用 lsp_document_symbols 获取文件符号结构
    - **AST 级别检查**：使用 ast_grep_search 精确匹配代码模式
    - **问题报告**：生成清晰的问题清单和修复建议
    - **交互式修复**：列出修复方案，用户确认后再执行

[执行流程]
    第一步：获取待检查文件
        - 如果用户指定了文件路径，使用指定路径
        - 如果未指定，使用 `git diff --name-only HEAD` 获取当前修改的文件
        - 过滤出 `.ts` 和 `.tsx` 文件
        - 如果没有找到 TS 文件，提示用户并结束

    第二步：读取规范文档
        - 读取 [REFERENCE_TYPESCRIPT_STYLE.md](REFERENCE_TYPESCRIPT_STYLE.md) 获取完整规范
        - 加载命名规范、Interface/Type 规范、代码简化策略

    第三步：分析文件结构
        - 使用 `lsp_document_symbols` 获取文件的符号列表（变量、函数、接口、类型）
        - 使用 `Read` 读取文件内容进行详细分析
        - 记录所有需要检查的符号及其位置

    第四步：检查命名规范
        使用 ast_grep_search 检查以下模式：
        
        4.1 变量命名检查（应为 snake_case）
            - 搜索 `const $VAR = $$$` 和 `let $VAR = $$$` 模式
            - 验证变量名是否符合 snake_case（允许短布尔变量如 isready）
            - 记录违规：变量名使用了 camelCase 或 PascalCase
        
        4.2 函数命名检查（应为 camelCase）
            - 搜索 `function $NAME($$$) { $$$ }` 模式
            - 搜索 `const $NAME = ($$$) => { $$$ }` 箭头函数模式
            - 验证函数名是否符合 camelCase
            - 记录违规：函数名使用了 snake_case 或 PascalCase

    第五步：检查 Interface/Type 规范
        5.1 Interface 检查（应只包含方法签名）
            - 搜索 `interface $NAME { $$$ }` 模式
            - 检查 interface 内部是否包含属性定义（非方法）
            - 记录违规：Interface 包含了属性定义，应改用 Type
        
        5.2 Type 检查（应用于数据结构）
            - 搜索 `type $NAME = { $$$ }` 模式
            - 检查 type 内部是否只包含方法签名
            - 记录违规：Type 只包含方法签名，应改用 Interface

    第六步：检查代码简化问题（可选）
        - 识别返回超过 5 个方法的工厂函数
        - 识别单个函数超过 50 行的情况
        - 识别职责混乱的迹象（一个函数做多件事）
        - 这部分作为建议，不作为强制规范

    第七步：生成检查报告
        按以下格式输出问题清单：
        
        ```
        ## TypeScript 规范检查报告
        
        **检查文件**: <文件路径>
        **检查时间**: <时间戳>
        **发现问题**: <数量>
        
        ### 命名规范问题
        
        | 行号 | 类型 | 当前命名 | 建议修改 | 规范说明 |
        |------|------|----------|----------|----------|
        | 12   | 变量 | userName | user_name | 变量应使用 snake_case |
        | 25   | 函数 | get_data | getData   | 函数应使用 camelCase |
        
        ### Interface/Type 规范问题
        
        | 行号 | 类型 | 问题描述 | 建议修改 |
        |------|------|----------|----------|
        | 45   | Interface | 包含属性定义 | 改用 Type 定义数据结构 |
        
        ### 代码简化建议（可选）
        
        - 第 100-180 行：函数 `createManager` 超过 50 行，建议拆分
        ```

    第八步：交互式修复
        - 展示问题清单后，询问用户："是否需要自动修复这些问题？(y/n)"
        - 如果用户确认：
            - 使用 `Edit` 工具逐个修复命名问题
            - 对于 Interface/Type 问题，展示修改前后对比，用户确认后再改
        - 如果用户拒绝：
            - 保留报告，不做任何修改
            - 提示用户可以手动修复

    第九步：输出完成信息
        - 显示已修复的问题数量
        - 显示剩余未修复的问题（如有）
        - 提示用户运行 `lsp_diagnostics` 验证修改后无语法错误

[注意事项]
    - **非破坏性检查**：默认只报告问题，不自动修改代码
    - **用户确认优先**：任何修改都需要用户明确确认
    - **命名规范细节**：
        - 变量：snake_case（如 `device_info`）
        - 短布尔变量：允许连写（如 `isready`、`hasdata`）
        - 函数/方法：camelCase（如 `calculateChecksum`）
    - **Interface vs Type**：
        - Interface：只定义方法签名（"能做什么"）
        - Type：定义属性结构和类型组合（"是什么"）
    - **代码简化**：这部分作为建议，不强制要求
    - **保持幂等性**：多次运行同一文件，结果应该一致
    - **错误处理**：如果 LSP 或 AST 工具失败，降级到正则匹配
    - **性能考虑**：只检查修改的文件，不扫描整个项目

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zdzdesigner) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
