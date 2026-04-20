---
name: bk-chat-x
description: 蓝鲸智云 AI Chat UI 组件库使用指南，提供 ChatInput/MessageContainer/ShortcutBtns 等纯 UI 组件。**必须与 bk-chat-helper skill 配合使用**：chat-x 负责 UI 渲染，chat-helper 负责业务逻辑和 API 调用。当用户开发 AI 小鲸、智能体、AI 聊天界面时，应同时参考这两个 skill。 Use when this capability is needed.
metadata:
  author: forrany
---

# @blueking/chat-x 组件库使用指南

## 快速开始

### 安装

```bash
pnpm add @blueking/chat-x
```

### 核心组件引入

```typescript
import {
  // 分子组件
  ChatInput,           // 聊天输入框
  MessageContainer,    // 消息容器
  MessageRender,       // 消息渲染器
  ContentRender,       // 内容渲染器
  ShortcutBtns,        // 快捷指令按钮组
  ShortcutRender,      // 快捷指令表单渲染
  AiSelection,         // AI 划词选择弹窗
  
  // 枚举和类型
  MessageRole,
  MessageStatus,
  MessageContentType,
  type Message,
  type Shortcut,
  type IToolBtn,
  type TagSchema,
} from '@blueking/chat-x';
```

## 核心组件

### 1. ChatInput 聊天输入框

```vue
<template>
  <ChatInput
    v-model="inputValue"
    v-model:cite="citeContent"
    :message-status="messageStatus"
    :placeholder="placeholder"
    :prompts="prompts"
    :resources="resources"
    :shortcuts="shortcuts"
    :shortcut-id="selectedShortcutId"
    :on-send-message="handleSendMessage"
    :on-stop-sending="handleStopSending"
    @select-shortcut="handleSelectShortcut"
    @delete-shortcut="handleDeleteShortcut"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { ChatInput, MessageStatus, type TagSchema, type Shortcut } from '@blueking/chat-x';

const inputValue = ref<string | TagSchema>('');
const citeContent = ref('');
const messageStatus = ref(MessageStatus.Complete);
const selectedShortcutId = ref<string | null>(null);

// Prompts (输入 / 触发)
const prompts = ref(['帮我写一篇文章', '解释这段代码']);

// Resources (输入 @ 触发)
const resources = ref([
  { id: 'file', name: '上传文件', type: 'file' },
  { id: 'image', name: '上传图片', type: 'image' },
]);

// Shortcuts 快捷指令
const shortcuts = ref<Shortcut[]>([
  { id: 'translate', name: '翻译' },
  { id: 'explain', name: '解释' },
]);

const handleSendMessage = async (value: string, docSchema: TagSchema) => {
  messageStatus.value = MessageStatus.Streaming;
  // 发送消息逻辑...
  messageStatus.value = MessageStatus.Complete;
};

const handleStopSending = async () => {
  messageStatus.value = MessageStatus.Stop;
};

const handleSelectShortcut = (shortcut: Shortcut, text?: string) => {
  selectedShortcutId.value = shortcut.id;
};

const handleDeleteShortcut = () => {
  selectedShortcutId.value = null;
};
</script>
```

**ChatInput Props:**

| 属性 | 类型 | 说明 |
|------|------|------|
| modelValue | `string \| TagSchema` | 输入值 (v-model) |
| cite | `string` | 引用内容 (v-model:cite) |
| messageStatus | `MessageStatus` | 控制按钮状态 |
| placeholder | `string` | 占位文本 |
| prompts | `string[]` | Prompt 列表 (/ 触发) |
| resources | `IAiSlashMenuItem[]` | 资源列表 (@ 触发) |
| shortcuts | `Shortcut[]` | 快捷指令列表 |
| shortcutId | `string` | 当前选中的快捷指令 ID |
| onSendMessage | `(value, docSchema) => Promise<void>` | 发送回调 |
| onStopSending | `() => Promise<void>` | 停止回调 |

### 2. MessageContainer 消息容器

```vue
<template>
  <MessageContainer
    v-model:selected-messages="selectedMessages"
    :messages="messages"
    :message-status="messageStatus"
    :enable-selection="enableSelection"
    :on-agent-action="handleAgentAction"
    :on-user-action="handleUserAction"
    @stop-streaming="handleStopStreaming"
  >
    <!-- 自定义消息渲染 (可选) -->
    <template #default="{ message }">
      <CustomMessage v-if="message.role === 'custom'" :message="message" />
      <MessageRender v-else :message="message" :on-action="handleAction" />
    </template>
  </MessageContainer>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import {
  MessageContainer,
  MessageRender,
  MessageRole,
  MessageStatus,
  type Message,
  type IToolBtn
} from '@blueking/chat-x';

const messages = ref<Message[]>([]);
const messageStatus = ref(MessageStatus.Complete);
const selectedMessages = ref<Message[]>([]);
const enableSelection = ref(false);

// AI 消息工具操作
const handleAgentAction = async (tool: IToolBtn) => {
  switch (tool.id) {
    case 'copy':
      // 复制由组件内部处理
      break;
    case 'like':
      return ['回答准确', '信息全面']; // 返回反馈原因列表
    case 'unlike':
      return ['信息错误', '回答不相关'];
    case 'rebuild':
      // 重新生成逻辑
      break;
  }
};

// 用户消息工具操作
const handleUserAction = async (tool: IToolBtn) => {
  switch (tool.id) {
    case 'edit':
      // 编辑逻辑
      break;
    case 'delete':
      // 删除逻辑
      break;
  }
};

const handleStopStreaming = () => {
  messageStatus.value = MessageStatus.Stop;
};
</script>
```

**MessageContainer Props:**

| 属性 | 类型 | 说明 |
|------|------|------|
| messages | `Message[]` | 消息列表 |
| messageStatus | `MessageStatus` | 当前消息状态 |
| enableSelection | `boolean` | 是否启用多选 |
| selectedMessages | `Message[]` | 选中的消息 (v-model) |
| onAgentAction | `(tool) => Promise<string[] \| void>` | AI 消息操作回调 |
| onUserAction | `(tool) => Promise<void>` | 用户消息操作回调 |

### 3. AiSelection 划词选择

```vue
<template>
  <AiSelection
    v-model:visible="selectionVisible"
    :shortcuts="shortcuts"
    :max-shortcut-count="3"
    :offset="10"
    @select-shortcut="handleSelectShortcut"
    @selection-change="handleSelectionChange"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { AiSelection, type Shortcut } from '@blueking/chat-x';

const selectionVisible = ref(false);
const shortcuts = ref<Shortcut[]>([
  { id: 'ask', name: '问问小鲸' },
  { id: 'translate', name: '翻译' },
]);

const handleSelectShortcut = (shortcut: Shortcut, selectedText: string) => {
  console.log('快捷指令:', shortcut.name, '选中文本:', selectedText);
};

const handleSelectionChange = (text: string) => {
  console.log('选区变化:', text);
};
</script>
```

### 4. ShortcutRender 快捷指令表单

```vue
<template>
  <ShortcutRender
    v-if="selectedShortcut?.components?.length"
    :name="selectedShortcut.name"
    :components="selectedShortcut.components"
    :form-model="selectedShortcut.formModel"
    @close="handleClose"
    @submit="handleSubmit"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { ShortcutRender, type Shortcut } from '@blueking/chat-x';

const selectedShortcut = ref<Shortcut>({
  id: 'translate',
  name: '翻译',
  components: [
    {
      type: 'select',
      key: 'targetLang',
      name: '目标语言',
      props: {
        options: [
          { label: '英文', value: 'en' },
          { label: '中文', value: 'zh' },
        ]
      }
    },
    {
      type: 'textarea',
      key: 'content',
      name: '翻译内容',
      fillBack: true,
    }
  ]
});

const handleClose = () => {
  selectedShortcut.value = null;
};

const handleSubmit = (formModel: Record<string, unknown>) => {
  console.log('提交表单:', formModel);
};
</script>
```

## 消息类型

```typescript
// 消息角色
enum MessageRole {
  User = 'user',           // 用户消息
  Assistant = 'assistant', // AI 助手
  Reasoning = 'reasoning', // 推理过程
  Tool = 'tool',           // 工具调用结果
  Activity = 'activity',   // 活动消息 (引用文档等)
  Info = 'info',           // 信息消息
  System = 'system',       // 系统消息
}

// 消息状态
enum MessageStatus {
  Pending = 'pending',     // 等待中
  Streaming = 'streaming', // 流式输出中
  Complete = 'complete',   // 完成
  Error = 'error',         // 错误
  Stop = 'stop',           // 已停止
  Disabled = 'disabled',   // 禁用
}
```

## 组件组合使用

典型的 AI 聊天页面结构：

```vue
<template>
  <div class="chat-page">
    <!-- 1. 空消息时显示快捷指令 -->
    <ShortcutBtns v-if="messages.length === 0" :shortcuts="shortcuts" />
    
    <!-- 2. 有消息时显示消息列表 -->
    <MessageContainer v-else :messages="messages" :message-status="messageStatus" />
    
    <!-- 3. 快捷指令表单 (与 ChatInput 互斥) -->
    <ShortcutRender v-if="selectedShortcut?.components?.length" v-bind="selectedShortcut" />
    
    <!-- 4. 输入框 -->
    <ChatInput v-else v-model="userInput" :on-send-message="handleSendMessage" />
    
    <!-- 5. 划词选择 (全局) -->
    <AiSelection v-model:visible="aiSelectionVisible" :shortcuts="shortcuts" />
  </div>
</template>
```

**关键交互流程：**

1. **发送消息**：`ChatInput.onSendMessage` → 添加用户消息 → 添加 AI 占位消息 → 流式更新
2. **快捷指令**：选择快捷指令 → 有表单则显示 `ShortcutRender`，无表单则直接发送
3. **划词操作**：选中文本 → `AiSelection` 显示 → 选择指令 → 执行或填充表单
4. **消息操作**：`onAgentAction` / `onUserAction` 处理复制、点赞、编辑等

> 完整实现代码见 [examples.md](examples.md)

## 自定义消息类型

通过声明合并扩展消息类型：

```typescript
// 在项目中声明
declare global {
  interface AIBluekingMessageMap {
    flow: BaseMessage<'flow', FlowContent>;
    custom: BaseMessage<'custom', CustomContent>;
  }
}

// 使用自定义消息
<MessageContainer :messages="messages">
  <template #default="{ message }">
    <FlowMessage v-if="message.role === 'flow'" :content="message.content" />
    <MessageRender v-else :message="message" />
  </template>
</MessageContainer>
```

## Composables 组合式函数

```typescript
import {
  useClipboard,              // 剪贴板操作
  useContainerScrollProvider, // 滚动控制 (Provider)
  useContainerScrollConsumer, // 滚动控制 (Consumer)
  useAnimationText,          // 文本动画
  useGlobalConfig,           // 全局配置
} from '@blueking/chat-x';

// 示例：剪贴板
const { copy } = useClipboard();
await copy('复制的文本');

// 示例：滚动控制
const { isScrollBottom, toScrollBottom } = useContainerScrollProvider(
  containerRef,
  bottomRef
);
```

## 更多资源

- 详细组件 API 参考: [references/components-api.md](references/components-api.md)
- 快捷指令配置: [references/shortcuts-guide.md](references/shortcuts-guide.md)
- 完整代码示例: [references/examples.md](references/examples.md)
- 主题定制: 查看 `packages/chat-x/wikis/theme/theme.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/forrany) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
