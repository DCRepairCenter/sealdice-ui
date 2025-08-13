# 组件文档

## 📦 组件概览

SealDice UI 采用组件化架构，将功能模块拆分为可复用的 Vue 组件。本文档详细介绍各个组件的使用方法和属性。

## 🎨 组件分类

### 页面组件（Pages）
路由级别的顶层组件，负责整体页面布局和业务逻辑。

### 功能组件（Features）
实现特定功能的业务组件，可在多个页面中复用。

### 工具组件（Utils）
提供通用功能的基础组件，不包含业务逻辑。

## 📄 核心页面组件

### Main.vue
**描述**：应用主布局组件，包含导航栏、侧边菜单和内容区域。

**主要功能**：
- 响应式布局管理
- 用户认证状态管理
- 全局通知显示
- 主题切换支持

**关键属性**：
```vue
<script setup lang="ts">
const store = useStore()
const password = ref('')
const dialogFeed = ref(false)
const drawerMenu = ref(false)
const advancedConfigCounter = ref(0)
</script>
```

### PageHome.vue
**描述**：系统主页组件，显示系统状态和日志。

**功能特性**：
- 系统状态监控
- 实时日志显示
- 网络健康检查
- 版本升级管理

**使用示例**：
```vue
<template>
  <PageHome />
</template>
```

### PageConnectInfoItems.vue
**描述**：IM 连接管理页面组件。

**功能特性**：
- 多平台连接配置
- 连接状态监控
- QR 码登录支持
- 连接测试功能

**表单类型定义**：
```typescript
interface addImConnectionForm {
  accountType: number
  nickname: string
  account: string
  password: string
  protocol: number
  // ... 更多字段
}
```

## 🧩 功能组件

### Menu.vue
**描述**：应用侧边栏菜单组件。

**属性**：
| 属性名 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| type | 'light' \| 'dark' | 'light' | 菜单主题 |
| advancedConfigCounter | number | 0 | 高级配置计数器 |

**使用示例**：
```vue
<Menu 
  v-model:advanced-config-counter="advancedConfigCounter" 
  type="dark" 
/>
```

### CustomTextBox.vue
**路径**：`src/components/customText/CustomTextBox.vue`

**描述**：自定义文案编辑框组件。

**功能特性**：
- 文本编辑
- 变量插入
- 实时预览
- 语法高亮

### BanList.vue & BanConfig.vue
**路径**：`src/components/misc/banList/`

**描述**：黑白名单管理组件。

**功能特性**：
- 列表展示
- 批量操作
- 导入导出
- 搜索过滤

## 🔧 工具组件

### diff-viewer.vue
**路径**：`src/components/utils/diff-viewer.vue`

**描述**：文本差异对比组件。

**属性**：
| 属性名 | 类型 | 说明 |
|--------|------|------|
| oldContent | string | 原始内容 |
| newContent | string | 新内容 |
| language | string | 语言类型 |

**使用示例**：
```vue
<DiffViewer 
  :old-content="originalText"
  :new-content="modifiedText"
  language="yaml"
/>
```

### foldable-card.vue
**路径**：`src/components/utils/foldable-card.vue`

**描述**：可折叠卡片组件。

**属性**：
| 属性名 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| title | string | '' | 卡片标题 |
| defaultExpanded | boolean | false | 默认展开状态 |

**插槽**：
- `header`: 自定义头部内容
- `default`: 卡片主体内容

### resource-render.vue
**路径**：`src/components/utils/resource-render.vue`

**描述**：资源渲染组件，支持图片、音频、视频等。

**属性**：
| 属性名 | 类型 | 说明 |
|--------|------|------|
| resource | Resource | 资源对象 |
| preview | boolean | 是否预览模式 |

### custom-reply-conditions.vue
**路径**：`src/components/utils/custom-reply-conditions.vue`

**描述**：自定义回复条件配置组件。

**功能特性**：
- 条件类型选择
- 条件值设置
- 逻辑组合
- 验证提示

### nested.vue
**路径**：`src/components/utils/nested.vue`

**描述**：嵌套结构渲染组件。

**使用场景**：
- 树形数据展示
- 递归结构渲染
- 层级关系显示

## 📂 模块组件

### 拦截管理模块组件

#### CensorConfig.vue
**描述**：拦截配置组件。

**功能**：
- 拦截级别设置
- 动作配置
- 白名单管理

#### CensorWords.vue
**描述**：敏感词管理组件。

**功能**：
- 敏感词列表
- 批量导入
- 分级管理

#### CensorLog.vue
**描述**：拦截日志查看组件。

**功能**：
- 日志列表
- 详情查看
- 统计分析

### 帮助文档模块组件

#### HelpConfigTags.vue
**描述**：帮助文档标签配置组件。

**功能**：
- 标签管理
- 分类设置
- 关联配置

### 跑团日志模块组件

#### StoryBackup.vue
**描述**：跑团日志备份组件。

**功能**：
- 日志导出
- 格式选择
- 批量处理

## 🎯 组件最佳实践

### 1. 组件命名规范

```vue
<!-- 页面组件：Page前缀 -->
<PageHome />

<!-- 功能组件：功能描述 -->
<BanList />

<!-- 工具组件：描述性名称 -->
<DiffViewer />
```

### 2. Props 定义规范

```typescript
interface Props {
  // 必需属性
  modelValue: string
  
  // 可选属性
  placeholder?: string
  
  // 带默认值的属性
  disabled?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  placeholder: '请输入',
  disabled: false
})
```

### 3. 事件发射规范

```typescript
const emit = defineEmits<{
  'update:modelValue': [value: string]
  'change': [value: string]
  'submit': []
}>()
```

### 4. 组件通信模式

#### 父子组件通信
```vue
<!-- 父组件 -->
<ChildComponent 
  v-model="value"
  @change="handleChange"
/>

<!-- 子组件 -->
<script setup>
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
</script>
```

#### 跨组件通信（使用 Pinia）
```typescript
// 在组件中
import { useStore } from '~/store'

const store = useStore()
// 读取状态
const config = computed(() => store.curDice.config)
// 调用action
await store.diceConfigSet(newConfig)
```

### 5. 组件生命周期管理

```vue
<script setup>
// 组件挂载
onMounted(async () => {
  await loadData()
  initializeComponent()
})

// 组件更新
watch(
  () => props.id,
  async (newId) => {
    await loadData(newId)
  }
)

// 组件卸载
onBeforeUnmount(() => {
  clearInterval(timerId)
  cleanup()
})
</script>
```

## 🔄 组件复用策略

### 1. 组合式函数（Composables）

```typescript
// composables/useDialog.ts
export function useDialog() {
  const visible = ref(false)
  
  const open = () => {
    visible.value = true
  }
  
  const close = () => {
    visible.value = false
  }
  
  return {
    visible,
    open,
    close
  }
}

// 在组件中使用
const { visible, open, close } = useDialog()
```

### 2. 插槽（Slots）使用

```vue
<!-- 定义插槽 -->
<template>
  <div class="card">
    <div class="card-header">
      <slot name="header" :title="title">
        <h3>{{ title }}</h3>
      </slot>
    </div>
    <div class="card-body">
      <slot />
    </div>
  </div>
</template>

<!-- 使用插槽 -->
<Card>
  <template #header="{ title }">
    <h2>自定义: {{ title }}</h2>
  </template>
  <p>卡片内容</p>
</Card>
```

### 3. 动态组件

```vue
<component 
  :is="currentComponent"
  v-bind="componentProps"
  @event="handleEvent"
/>

<script setup>
const components = {
  PageHome,
  PageAbout,
  PageSettings
}

const currentComponent = computed(() => 
  components[route.name]
)
</script>
```

## 📊 性能优化

### 1. 异步组件

```typescript
const AsyncComponent = defineAsyncComponent(() =>
  import('./HeavyComponent.vue')
)
```

### 2. 组件缓存

```vue
<KeepAlive>
  <component :is="view" />
</KeepAlive>
```

### 3. 虚拟列表

```vue
<VirtualList
  :items="largeDataset"
  :item-height="50"
  :visible-items="10"
>
  <template #default="{ item }">
    <ListItem :data="item" />
  </template>
</VirtualList>
```

## 🐛 组件调试

### Vue DevTools 使用

1. 安装浏览器扩展
2. 打开开发者工具
3. 切换到 Vue 标签
4. 查看组件树和状态

### 调试技巧

```vue
<script setup>
// 开发环境日志
if (import.meta.env.DEV) {
  console.log('Component mounted:', props)
}

// 使用 watchEffect 追踪响应式依赖
watchEffect(() => {
  console.log('Dependencies changed:', {
    prop: props.value,
    state: state.value
  })
})
</script>
```

## 📚 组件开发资源

### 相关文档
- [Vue 3 组件基础](https://cn.vuejs.org/guide/essentials/component-basics.html)
- [Element Plus 组件](https://element-plus.org/zh-CN/component/button.html)
- [VueUse 组合式函数](https://vueuse.org/)

### 代码示例
组件开发的完整示例可以在项目的 `src/components` 目录中找到。

---

> 💡 **提示**：组件是应用的基础构建块，良好的组件设计能够提高代码复用性和可维护性。遵循单一职责原则，保持组件的简洁和专注。