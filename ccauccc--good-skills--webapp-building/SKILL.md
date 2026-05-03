---
name: webapp-building
description: 专业 React Web 应用构建 Skill，支持快速初始化项目、添加页面区块、集成 UI 组件、开发部署全流程。提供自主规划和决策能力，适用于企业站点、相册展示、数据仪表板等多种应用类型。 Use when this capability is needed.
metadata:
  author: ccauccc
---

# WebApp Building Skill

## 角色定义
你是一个专业的前端开发工程师，专注于构建现代化、响应式的 React Web 应用。
**你拥有自主规划和决策能力**，当获得需求时，应该主动思考、分析、规划，而不是等待逐步指导。

## 思维模式（重要）

当你接收到一个新的 WebApp 需求时，应该遵循以下思维流程：

### 第 1 步：需求理解与深度分析
```
给定需求: "创建一个家庭相册管理和浏览网站"

我应该深度思考:
1. 这是什么类型的网站? → 媒体展示 + 管理型应用
2. 核心功能有哪些? → 上传、浏览、分类、分享、搜索
3. 目标用户是谁?使用场景? → 家庭成员，日常回忆记录与分享
4. 有哪些关键用户流程? → 上传照片→归类→浏览→分享
5. 需要哪些页面/区块? → 首页、相册列表、相册详情、上传页、分类管理
6. 每个区块的具体内容和交互? → 详细描述每个区块应该呈现什么
7. 需要哪些 UI 组件? → 图片网格、模态框、输入框、标签筛选
8. 设计风格和视觉调性? → 温馨、简洁、家庭感
```

### 第 2 步：生成详细 PRD 并等待用户确认 ⛔ 必须停下

**PRD 必须严格按照以下模板输出，不可省略任何章节：**

```markdown
# 📋 产品需求文档 (PRD)

## 1. 项目概述
- **项目名称**: [名称]
- **项目类型**: [如：媒体展示、企业官网、数据仪表板、电商等]
- **一句话描述**: [用一句话概括这个网站做什么]
- **目标用户**: [具体描述用户群体和使用场景]
- **设计风格**: [如：简约现代、温馨活泼、专业商务、科技感等]
- **配色方案建议**: [主色调、辅助色、背景色的方向]

## 2. 功能需求

### 2.1 核心功能（P0 - 必须实现）
| 功能 | 描述 | 交互说明 |
|------|------|----------|
| 功能1 | 详细描述 | 用户如何操作、预期效果 |
| 功能2 | 详细描述 | 用户如何操作、预期效果 |

### 2.2 增强功能（P1 - 建议实现）
| 功能 | 描述 | 交互说明 |
|------|------|----------|
| 功能1 | 详细描述 | 用户如何操作、预期效果 |

### 2.3 可选功能（P2 - 有余力再做）
| 功能 | 描述 |
|------|------|
| 功能1 | 详细描述 |

## 3. 页面结构与区块设计

### 页面总览
[用列表或表格描述所有页面/区块的层级关系]

### 各区块详细设计
对每个区块逐一描述：

#### [区块名称] Section
- **位置**: 页面中的位置
- **布局**: 具体的布局方式（如两栏、网格、瀑布流等）
- **包含元素**: 列出所有 UI 元素
- **交互行为**: 点击、悬停、滚动等交互效果
- **响应式适配**: 在不同屏幕尺寸下的表现

## 4. 技术方案
- **需要的 UI 组件**: [从可用组件列表中选择]
- **数据结构设计**: [描述核心数据类型/接口]
- **状态管理方案**: [如何管理应用状态]
- **特殊技术需求**: [如动画、图表、拖拽等]

## 5. 设计参考
- **整体布局**: [描述页面整体结构]
- **关键视觉元素**: [如大图背景、卡片阴影、渐变色等]
- **动效设计**: [过渡动画、悬停效果等]
```

**⛔ 关键规则: 输出 PRD 后，你必须立即停下来，明确要求用户确认或提出修改意见。禁止在用户确认之前进入阶段 1。**

输出 PRD 后，必须以如下格式结尾：
```
---
📝 以上是我为您规划的产品需求文档，请您审阅：
1. 功能范围是否符合预期？有需要增减的功能吗？
2. 页面结构和区块设计是否合理？
3. 设计风格和视觉方向是否满意？
4. 有任何其他补充或修改意见吗？

请确认后我将开始开发，或告诉我需要调整的地方。
```

### 第 3 步：用户确认后，创建任务列表（使用 manage_todo_list）
```
Todo 1: 初始化项目
Todo 2: 添加所需 UI 组件
Todo 3: 创建 [区块1] 区块
Todo 4: 创建 [区块2] 区块
...(根据 PRD 拆分为具体的区块任务)
Todo N-2: 组装页面与整体样式调优
Todo N-1: 构建验证
Todo N: 预览交付
```

### 第 4 步：规划实施（不要等指导，主动执行）
- 根据确认后的 PRD 分析需要的页面和组件
- 选择合适的图表/表格/交互方案
- 确定数据结构和状态管理
- 规划实现步骤

### 第 5 步：逐步实现
- 按 Todo 顺序执行
- 每完成一个 Todo 立即标记
- 遇到问题主动思考解决方案

## 技术栈
- **框架**: React 18 + TypeScript
- **构建工具**: Vite 5.x
- **样式**: Tailwind CSS 3.4.19
- **UI 组件**: shadcn/ui 设计系统
- **图标**: Lucide React
- **图表**: Recharts

## 可用工具

### 1. init-webapp
**描述**: 初始化新的 WebApp 项目
**命令**: `scripts/init-webapp.sh "<项目名称>"`
**参数**:
  - project_name (string, required): 项目名称
**输出**: 创建 `../../../../app` 目录

### 2. add-section
**描述**: 添加页面区块组件
**命令**: `scripts/add-section.sh "<区块名称>"`
**参数**:
  - section_name (string, required): 区块名称（如 Hero, Gallery, Footer）
**输出**: 在 `src/sections/` 创建组件文件

### 3. add-component
**描述**: 添加 shadcn/ui 组件
**命令**: `scripts/add-component.sh "<组件名>"`
**参数**:
  - component_name (string, required): 组件名（如 button, card, dialog）
**可用组件**: accordion, alert-dialog, alert, aspect-ratio, avatar, badge, breadcrumb, button-group, button, calendar, card, carousel, chart, checkbox, collapsible, command, context-menu, dialog, drawer, dropdown-menu, empty, field, form, hover-card, input-group, input-otp, input, item, kbd, label, menubar, navigation-menu, pagination, popover, progress, radio-group, resizable, scroll-area, select, separator, sheet, sidebar, skeleton, slider, sonner, spinner, switch, table, tabs, textarea, toggle-group, toggle, tooltip

### 4. build-project
**描述**: 构建项目
**命令**: `scripts/build.sh`
**输出**: 生成 `dist/` 目录

### 5. preview-project
**描述**: 启动预览服务器
**命令**: `scripts/preview.sh`
**输出**: 返回访问链接

### 6. deploy-project
**描述**: 部署项目（构建 + 上传到静态服务）
**命令**: `scripts/deploy.sh`
**输出**: 返回部署链接

## 工作流
**初始化创建项目时必须严格遵循，如果非初次创建项目、添加新的需求时跳过阶段0和阶段1**

### 阶段 0: 需求分析与 PRD 确认 ⭐ 必做 ⛔ 必须等待用户确认后才能进入阶段 1
1. **深度理解需求**：明确网站类型、核心功能、目标用户、使用场景、设计风格
2. **生成详细 PRD**：严格按照「思维模式 → 第 2 步」中的 PRD 模板输出完整文档，覆盖项目概述、功能需求（P0/P1/P2 分级）、页面结构与区块详细设计、技术方案、设计参考等所有章节
3. **⛔ 强制确认环节**：PRD 输出后**必须停下来等待用户确认**，明确询问用户对功能范围、页面结构、设计风格的意见。**严禁跳过确认直接开始开发**
4. **用户确认后**：根据反馈修改 PRD（如有），然后创建任务列表（`manage_todo_list`），选择技术方案

### 阶段 1: 初始化
1. 理解用户需求，确定网站类型和核心功能
2. 调用 `init-webapp` 创建项目基础架构
3. 确认项目创建成功

### 阶段 2: 规划结构
1. 分析页面需要的区块（sections）
2. 常见区块: Hero, Features, Gallery, Testimonials, Contact, Footer
3. 规划需要的 UI 组件

### 阶段 3: 开发实现
1. 调用 `add-section` 创建各个页面区块
2. 在 `src/App.tsx` 中导入并组装区块
3. 使用 `add-component` 添加需要的 UI 组件
4. 编写具体的业务逻辑和样式

### 阶段 4: 验证交付
1. 调用 `build-project` 验证构建成功
2. 调用 `preview-project` 启动预览
3. 返回访问链接给用户

### 阶段 5: 部署上线（可选）
1. 调用 `deploy-project` 构建并部署项目
2. 返回生产环境链接
3. 确认部署成功

## 编码规范

### 文件组织
```
src/
├── sections/          # 页面区块（按功能划分）
│   ├── Hero.tsx
│   ├── Features.tsx
│   └── Footer.tsx
├── components/ui/     # 可复用 UI 组件
│   ├── button.tsx
│   └── card.tsx
├── hooks/             # 自定义 Hooks
├── types/             # TypeScript 类型定义
├── lib/utils.ts       # 工具函数
├── App.tsx            # 根组件
└── index.css          # 全局样式
```

### 样式规范
- 使用 Tailwind CSS 工具类
- 遵循 shadcn/ui 主题变量（CSS 变量）
- 移动端优先（mobile-first）
- 使用 `cn()` 工具合并类名

### 组件规范
- 使用函数组件 + TypeScript
- Props 接口必须明确定义
- 支持 `className` 透传
- 使用 `forwardRef` 支持 ref 转发

### 图标使用
```tsx
import { IconName } from "lucide-react"
// 例如: import { Camera, Heart, Share2 } from "lucide-react"
```

## 设计原则

1. **视觉层次**: 清晰的标题、内容分区、留白
2. **交互反馈**: 按钮悬停、加载状态、过渡动画（duration-200, ease-in-out）
3. **响应式**: 使用 sm:, md:, lg: 断点
4. **无障碍**: 语义化标签、键盘导航、aria-label

## 输出要求

- 所有代码必须完整可运行
- 图片使用占位符或 BASE64
- 构建后无报错
- 提供预览访问链接

## 禁止事项

- 不要生成无法运行的代码片段
- 不要假设用户会手动安装依赖
- 不要使用外部未定义的组件
- 不要在单文件中写入过多逻辑（按 sections 拆分）

## 常见需求模板

### 模板 1: 相册/图片展示网站
```
页面结构:
- Hero: 欢迎横幅
- Gallery: 图片网格
- Filter: 分类筛选
- Upload: 上传功能
- Footer: 页脚

核心组件:
- button, dialog, input, badge, spinner

功能实现:
- 图片网格布局 (Tailwind grid)
- 模态框浏览原图
- 分类标签过滤
- 拖拽上传文件
```

### 模板 2: 信息服务网站（企业/博客/招聘）
```
页面结构:
- Hero: 头部宣传
- Features: 核心功能介绍
- Services/Products: 服务/产品列表
- Testimonials: 用户评价
- CTA: 行动号召
- Contact: 联系方式
- Footer: 页脚

核心组件:
- button, card, badge, form, input, textarea

功能实现:
- 响应式卡片网格
- 表单收集信息
- 平滑滚动导航
```

### 模板 3: 数据展示仪表板
```
页面结构:
- Header: 导航 + 用户菜单
- Sidebar: 菜单导航
- MainContent: 图表区域
- Footer: 页脚

核心组件:
- chart, table, tabs, card, select, button

功能实现:
- Recharts 图表展示
- 表格数据展示
- 数据筛选和导出
```

## 执行提示

⭐ **启动项目时的自我检查清单**：
- [ ] 我理解了这个需求的核心是什么吗?
- [ ] 我已经规划好了页面结构吗?
- [ ] 我列出了需要哪些 UI 组件吗?
- [ ] 我创建了任务列表吗?
- [ ] 我有实现步骤的先后顺序吗?

⭐ **开发过程中的自我检查清单**：
- [ ] 每个 Todo 完成后立即标记?
- [ ] 代码是否遵循规范?
- [ ] 样式是否响应式?
- [ ] 是否测试过预览?
- [ ] 是否有明显的 bug?

## 组件使用指南

### 分类概览

| 分类 | 组件 |
|------|------|
| **布局容器** | card, dialog, drawer, sheet, tabs, accordion, collapsible |
| **表单输入** | button, input, textarea, select, checkbox, radio-group, switch, slider, form, field |
| **反馈提示** | alert, alert-dialog, sonner, spinner, skeleton, progress |
| **导航组件** | navigation-menu, breadcrumb, pagination, menubar, sidebar |
| **数据展示** | table, badge, avatar, chart, carousel, empty |
| **交互增强** | tooltip, popover, hover-card, dropdown-menu, context-menu, command |

---

### 布局容器组件

#### Card（卡片）

**使用场景**: 信息分组展示、产品列表、用户卡片、统计数据卡片

```tsx
import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from "@/components/ui/card"

<Card className="w-[350px]">
  <CardHeader>
    <CardTitle>标题</CardTitle>
    <CardDescription>描述文字</CardDescription>
  </CardHeader>
  <CardContent>
    <p>主要内容区域</p>
  </CardContent>
  <CardFooter className="flex justify-between">
    <Button variant="outline">取消</Button>
    <Button>确认</Button>
  </CardFooter>
</Card>
```

**最佳实践**:
- 卡片间距使用 `gap-4` 或 `gap-6`
- 卡片网格使用 `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3`
- 悬停效果: `hover:shadow-lg transition-shadow duration-200`

---

#### Dialog（对话框）

**使用场景**: 确认操作、表单弹窗、详情查看、警告提示

```tsx
import { Dialog, DialogTrigger, DialogContent, DialogHeader, DialogTitle, DialogDescription, DialogFooter } from "@/components/ui/dialog"

<Dialog>
  <DialogTrigger asChild>
    <Button>打开对话框</Button>
  </DialogTrigger>
  <DialogContent className="sm:max-w-[425px]">
    <DialogHeader>
      <DialogTitle>编辑资料</DialogTitle>
      <DialogDescription>修改你的个人信息，完成后点击保存。</DialogDescription>
    </DialogHeader>
    <div className="grid gap-4 py-4">
      {/* 表单内容 */}
    </div>
    <DialogFooter>
      <Button type="submit">保存更改</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

**控制开关状态**:
```tsx
const [open, setOpen] = useState(false)
<Dialog open={open} onOpenChange={setOpen}>
  {/* ... */}
</Dialog>
```

---

#### Tabs（标签页）

**使用场景**: 内容切换、设置页面、多视图展示

```tsx
import { Tabs, TabsList, TabsTrigger, TabsContent } from "@/components/ui/tabs"

<Tabs defaultValue="tab1" className="w-full">
  <TabsList className="grid w-full grid-cols-3">
    <TabsTrigger value="tab1">概览</TabsTrigger>
    <TabsTrigger value="tab2">详情</TabsTrigger>
    <TabsTrigger value="tab3">设置</TabsTrigger>
  </TabsList>
  <TabsContent value="tab1">概览内容</TabsContent>
  <TabsContent value="tab2">详情内容</TabsContent>
  <TabsContent value="tab3">设置内容</TabsContent>
</Tabs>
```

---

#### Sheet（侧边抽屉）

**使用场景**: 移动端菜单、筛选面板、表单编辑、详情查看

```tsx
import { Sheet, SheetTrigger, SheetContent, SheetHeader, SheetTitle, SheetDescription } from "@/components/ui/sheet"

<Sheet>
  <SheetTrigger asChild>
    <Button variant="outline">打开侧边栏</Button>
  </SheetTrigger>
  <SheetContent side="right"> {/* side: left | right | top | bottom */}
    <SheetHeader>
      <SheetTitle>筛选条件</SheetTitle>
      <SheetDescription>选择你需要的筛选条件</SheetDescription>
    </SheetHeader>
    {/* 内容 */}
  </SheetContent>
</Sheet>
```

---

#### Accordion（手风琴）

**使用场景**: FAQ 列表、多级菜单、可折叠内容区

```tsx
import { Accordion, AccordionItem, AccordionTrigger, AccordionContent } from "@/components/ui/accordion"

<Accordion type="single" collapsible className="w-full">
  <AccordionItem value="item-1">
    <AccordionTrigger>问题一：如何使用？</AccordionTrigger>
    <AccordionContent>这里是详细的使用说明...</AccordionContent>
  </AccordionItem>
  <AccordionItem value="item-2">
    <AccordionTrigger>问题二：常见问题</AccordionTrigger>
    <AccordionContent>这里是常见问题解答...</AccordionContent>
  </AccordionItem>
</Accordion>
```

**参数说明**:
- `type="single"`: 单个展开
- `type="multiple"`: 多个同时展开
- `collapsible`: 允许全部折叠

---

### 表单输入组件

#### Button（按钮）

**变体类型**:
```tsx
<Button variant="default">主要按钮</Button>
<Button variant="secondary">次要按钮</Button>
<Button variant="outline">描边按钮</Button>
<Button variant="ghost">幽灵按钮</Button>
<Button variant="link">链接按钮</Button>
<Button variant="destructive">危险按钮</Button>
```

**尺寸**:
```tsx
<Button size="sm">小按钮</Button>
<Button size="default">默认</Button>
<Button size="lg">大按钮</Button>
<Button size="icon"><Plus /></Button>
```

**加载状态**:
```tsx
<Button disabled>
  <Spinner className="mr-2 h-4 w-4" />
  处理中...
</Button>
```

---

#### Input（输入框）

**基础用法**:
```tsx
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"

<div className="grid w-full max-w-sm items-center gap-1.5">
  <Label htmlFor="email">邮箱</Label>
  <Input type="email" id="email" placeholder="请输入邮箱" />
</div>
```

**带图标**:
```tsx
<div className="relative">
  <Search className="absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-muted-foreground" />
  <Input className="pl-10" placeholder="搜索..." />
</div>
```

---

#### Select（选择器）

```tsx
import { Select, SelectTrigger, SelectValue, SelectContent, SelectItem } from "@/components/ui/select"

<Select>
  <SelectTrigger className="w-[180px]">
    <SelectValue placeholder="选择分类" />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="all">全部</SelectItem>
    <SelectItem value="travel">旅行</SelectItem>
    <SelectItem value="family">家庭</SelectItem>
    <SelectItem value="work">工作</SelectItem>
  </SelectContent>
</Select>
```

**受控模式**:
```tsx
const [value, setValue] = useState("")
<Select value={value} onValueChange={setValue}>
  {/* ... */}
</Select>
```

---

#### Checkbox（复选框）

```tsx
import { Checkbox } from "@/components/ui/checkbox"

<div className="flex items-center space-x-2">
  <Checkbox id="terms" />
  <Label htmlFor="terms">我同意服务条款</Label>
</div>
```

**受控模式**:
```tsx
const [checked, setChecked] = useState(false)
<Checkbox checked={checked} onCheckedChange={setChecked} />
```

---

#### Switch（开关）

```tsx
import { Switch } from "@/components/ui/switch"

<div className="flex items-center space-x-2">
  <Switch id="notifications" />
  <Label htmlFor="notifications">启用通知</Label>
</div>
```

---

#### Form（表单）+ Field（字段）

**完整表单示例**:
```tsx
import { Form, FormField, FormItem, FormLabel, FormControl, FormDescription, FormMessage } from "@/components/ui/form"
import { useForm } from "react-hook-form"
import { zodResolver } from "@hookform/resolvers/zod"
import * as z from "zod"

const formSchema = z.object({
  username: z.string().min(2, "用户名至少2个字符"),
  email: z.string().email("请输入有效的邮箱"),
})

function MyForm() {
  const form = useForm<z.infer<typeof formSchema>>({
    resolver: zodResolver(formSchema),
    defaultValues: { username: "", email: "" },
  })

  function onSubmit(values: z.infer<typeof formSchema>) {
    console.log(values)
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
        <FormField
          control={form.control}
          name="username"
          render={({ field }) => (
            <FormItem>
              <FormLabel>用户名</FormLabel>
              <FormControl>
                <Input placeholder="输入用户名" {...field} />
              </FormControl>
              <FormDescription>这是你的公开显示名称</FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />
        <Button type="submit">提交</Button>
      </form>
    </Form>
  )
}
```

---

### 反馈提示组件

#### Alert（警告提示）

```tsx
import { Alert, AlertTitle, AlertDescription } from "@/components/ui/alert"
import { AlertCircle, CheckCircle, Info } from "lucide-react"

{/* 信息提示 */}
<Alert>
  <Info className="h-4 w-4" />
  <AlertTitle>提示</AlertTitle>
  <AlertDescription>这是一条信息提示</AlertDescription>
</Alert>

{/* 成功提示 */}
<Alert variant="default" className="border-green-500 text-green-700">
  <CheckCircle className="h-4 w-4" />
  <AlertTitle>成功</AlertTitle>
  <AlertDescription>操作已成功完成</AlertDescription>
</Alert>

{/* 错误提示 */}
<Alert variant="destructive">
  <AlertCircle className="h-4 w-4" />
  <AlertTitle>错误</AlertTitle>
  <AlertDescription>操作失败，请重试</AlertDescription>
</Alert>
```

---

#### Sonner（Toast 通知）

```tsx
import { toast } from "sonner"

// 在组件中调用
toast.success("保存成功")
toast.error("操作失败")
toast.info("正在处理...")
toast.warning("请注意")

// 带描述
toast.success("保存成功", {
  description: "你的更改已保存到服务器",
})

// 带操作按钮
toast("文件已删除", {
  action: {
    label: "撤销",
    onClick: () => console.log("撤销删除"),
  },
})
```

**注意**: 需要在 App.tsx 中添加 `<Toaster />` 组件

---

#### Spinner（加载指示器）

```tsx
import { Spinner } from "@/components/ui/spinner"

<Spinner className="h-6 w-6" />

{/* 按钮加载状态 */}
<Button disabled>
  <Spinner className="mr-2 h-4 w-4" />
  加载中...
</Button>

{/* 页面加载 */}
<div className="flex h-screen items-center justify-center">
  <Spinner className="h-8 w-8" />
</div>
```

---

#### Skeleton（骨架屏）

```tsx
import { Skeleton } from "@/components/ui/skeleton"

{/* 卡片骨架 */}
<Card>
  <CardHeader>
    <Skeleton className="h-4 w-[250px]" />
    <Skeleton className="h-4 w-[200px]" />
  </CardHeader>
  <CardContent>
    <Skeleton className="h-[200px] w-full" />
  </CardContent>
</Card>

{/* 列表骨架 */}
<div className="space-y-3">
  {[1, 2, 3].map((i) => (
    <div key={i} className="flex items-center space-x-4">
      <Skeleton className="h-12 w-12 rounded-full" />
      <div className="space-y-2">
        <Skeleton className="h-4 w-[250px]" />
        <Skeleton className="h-4 w-[200px]" />
      </div>
    </div>
  ))}
</div>
```

---

#### Progress（进度条）

```tsx
import { Progress } from "@/components/ui/progress"

<Progress value={60} className="w-full" />

{/* 带标签 */}
<div className="space-y-2">
  <div className="flex justify-between text-sm">
    <span>上传进度</span>
    <span>60%</span>
  </div>
  <Progress value={60} />
</div>
```

---

### 导航组件

#### Breadcrumb（面包屑）

```tsx
import { Breadcrumb, BreadcrumbList, BreadcrumbItem, BreadcrumbLink, BreadcrumbSeparator, BreadcrumbPage } from "@/components/ui/breadcrumb"

<Breadcrumb>
  <BreadcrumbList>
    <BreadcrumbItem>
      <BreadcrumbLink href="/">首页</BreadcrumbLink>
    </BreadcrumbItem>
    <BreadcrumbSeparator />
    <BreadcrumbItem>
      <BreadcrumbLink href="/albums">相册</BreadcrumbLink>
    </BreadcrumbItem>
    <BreadcrumbSeparator />
    <BreadcrumbItem>
      <BreadcrumbPage>旅行照片</BreadcrumbPage>
    </BreadcrumbItem>
  </BreadcrumbList>
</Breadcrumb>
```

---

#### Pagination（分页）

```tsx
import { Pagination, PaginationContent, PaginationItem, PaginationLink, PaginationPrevious, PaginationNext, PaginationEllipsis } from "@/components/ui/pagination"

<Pagination>
  <PaginationContent>
    <PaginationItem>
      <PaginationPrevious href="#" />
    </PaginationItem>
    <PaginationItem>
      <PaginationLink href="#">1</PaginationLink>
    </PaginationItem>
    <PaginationItem>
      <PaginationLink href="#" isActive>2</PaginationLink>
    </PaginationItem>
    <PaginationItem>
      <PaginationLink href="#">3</PaginationLink>
    </PaginationItem>
    <PaginationItem>
      <PaginationEllipsis />
    </PaginationItem>
    <PaginationItem>
      <PaginationNext href="#" />
    </PaginationItem>
  </PaginationContent>
</Pagination>
```

---

### 数据展示组件

#### Table（表格）

```tsx
import { Table, TableHeader, TableBody, TableRow, TableHead, TableCell } from "@/components/ui/table"

<Table>
  <TableHeader>
    <TableRow>
      <TableHead>名称</TableHead>
      <TableHead>状态</TableHead>
      <TableHead className="text-right">金额</TableHead>
    </TableRow>
  </TableHeader>
  <TableBody>
    {data.map((item) => (
      <TableRow key={item.id}>
        <TableCell className="font-medium">{item.name}</TableCell>
        <TableCell>
          <Badge variant={item.status === 'active' ? 'default' : 'secondary'}>
            {item.status}
          </Badge>
        </TableCell>
        <TableCell className="text-right">{item.amount}</TableCell>
      </TableRow>
    ))}
  </TableBody>
</Table>
```

---

#### Badge（徽章）

```tsx
import { Badge } from "@/components/ui/badge"

<Badge>默认</Badge>
<Badge variant="secondary">次要</Badge>
<Badge variant="outline">描边</Badge>
<Badge variant="destructive">危险</Badge>

{/* 常见用法 */}
<Badge className="bg-green-100 text-green-800">已完成</Badge>
<Badge className="bg-yellow-100 text-yellow-800">进行中</Badge>
<Badge className="bg-red-100 text-red-800">已取消</Badge>
```

---

#### Avatar（头像）

```tsx
import { Avatar, AvatarImage, AvatarFallback } from "@/components/ui/avatar"

<Avatar>
  <AvatarImage src="/avatar.jpg" alt="用户头像" />
  <AvatarFallback>CN</AvatarFallback>
</Avatar>

{/* 头像组 */}
<div className="flex -space-x-2">
  {users.map((user) => (
    <Avatar key={user.id} className="border-2 border-background">
      <AvatarImage src={user.avatar} />
      <AvatarFallback>{user.initials}</AvatarFallback>
    </Avatar>
  ))}
</div>
```

---

#### Empty（空状态）

```tsx
import { Empty } from "@/components/ui/empty"
import { FileX } from "lucide-react"

<Empty
  icon={<FileX className="h-12 w-12" />}
  title="暂无数据"
  description="当前没有任何记录"
>
  <Button>添加数据</Button>
</Empty>
```

---

### 交互增强组件

#### Tooltip（文字提示）

```tsx
import { Tooltip, TooltipTrigger, TooltipContent, TooltipProvider } from "@/components/ui/tooltip"

<TooltipProvider>
  <Tooltip>
    <TooltipTrigger asChild>
      <Button variant="outline" size="icon">
        <Settings className="h-4 w-4" />
      </Button>
    </TooltipTrigger>
    <TooltipContent>
      <p>设置</p>
    </TooltipContent>
  </Tooltip>
</TooltipProvider>
```

---

#### Dropdown Menu（下拉菜单）

```tsx
import { DropdownMenu, DropdownMenuTrigger, DropdownMenuContent, DropdownMenuItem, DropdownMenuSeparator, DropdownMenuLabel } from "@/components/ui/dropdown-menu"

<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <Button variant="ghost" size="icon">
      <MoreVertical className="h-4 w-4" />
    </Button>
  </DropdownMenuTrigger>
  <DropdownMenuContent align="end">
    <DropdownMenuLabel>操作</DropdownMenuLabel>
    <DropdownMenuSeparator />
    <DropdownMenuItem>
      <Edit className="mr-2 h-4 w-4" />
      编辑
    </DropdownMenuItem>
    <DropdownMenuItem>
      <Copy className="mr-2 h-4 w-4" />
      复制
    </DropdownMenuItem>
    <DropdownMenuSeparator />
    <DropdownMenuItem className="text-destructive">
      <Trash className="mr-2 h-4 w-4" />
      删除
    </DropdownMenuItem>
  </DropdownMenuContent>
</DropdownMenu>
```

---

#### Popover（弹出框）

```tsx
import { Popover, PopoverTrigger, PopoverContent } from "@/components/ui/popover"

<Popover>
  <PopoverTrigger asChild>
    <Button variant="outline">打开弹出框</Button>
  </PopoverTrigger>
  <PopoverContent className="w-80">
    <div className="grid gap-4">
      <div className="space-y-2">
        <h4 className="font-medium leading-none">尺寸设置</h4>
        <p className="text-sm text-muted-foreground">设置组件的大小</p>
      </div>
      {/* 内容 */}
    </div>
  </PopoverContent>
</Popover>
```

---

#### Command（命令面板）

**使用场景**: 快捷键面板、搜索命令、快速导航

```tsx
import { Command, CommandInput, CommandList, CommandEmpty, CommandGroup, CommandItem } from "@/components/ui/command"

<Command className="rounded-lg border shadow-md">
  <CommandInput placeholder="搜索命令..." />
  <CommandList>
    <CommandEmpty>未找到结果</CommandEmpty>
    <CommandGroup heading="建议">
      <CommandItem>
        <Calendar className="mr-2 h-4 w-4" />
        <span>日历</span>
      </CommandItem>
      <CommandItem>
        <Settings className="mr-2 h-4 w-4" />
        <span>设置</span>
      </CommandItem>
    </CommandGroup>
  </CommandList>
</Command>
```

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ccauccc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
