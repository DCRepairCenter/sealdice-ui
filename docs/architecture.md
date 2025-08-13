# 技术架构

## 🏗️ 系统架构概览

SealDice UI 采用现代化的前端架构，基于 Vue 3 生态系统构建，提供了高性能、可维护和可扩展的用户界面。

```
┌─────────────────────────────────────────────────────┐
│                    用户浏览器                        │
└─────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────┐
│                  SealDice UI (Vue 3)                │
│  ┌─────────────────────────────────────────────┐   │
│  │              Vue Components                  │   │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐      │   │
│  │  │  Pages  │ │ Modules │ │  Utils  │      │   │
│  │  └─────────┘ └─────────┘ └─────────┘      │   │
│  └─────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────┐   │
│  │            State Management (Pinia)         │   │
│  └─────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────┐   │
│  │               Router (Vue Router)           │   │
│  └─────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────┐   │
│  │              API Layer (Axios)              │   │
│  └─────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
                            │
                     WebSocket/HTTP
                            │
                            ▼
┌─────────────────────────────────────────────────────┐
│              SealDice Core (Go Backend)             │
│  ┌─────────────────────────────────────────────┐   │
│  │            RESTful API Services             │   │
│  └─────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────┐   │
│  │              WebSocket Services             │   │
│  └─────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────┐   │
│  │                 Database                    │   │
│  └─────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

## 📦 核心技术选型

### 构建工具
- **Vite 7.0+**
  - 极速的冷启动
  - 即时热模块更新（HMR）
  - 优化的构建性能
  - 原生 ES 模块支持

### 前端框架
- **Vue 3.5+**
  - Composition API 提供更好的逻辑复用
  - 更好的 TypeScript 支持
  - 性能优化（Proxy-based 响应式系统）
  - Tree-shaking 支持

### 编程语言
- **TypeScript**
  - 静态类型检查
  - 更好的 IDE 支持
  - 降低运行时错误
  - 提升代码可维护性

### UI 组件库
- **Element Plus 2.10+**
  - 丰富的组件库
  - 完善的主题定制
  - 良好的文档支持
  - TypeScript 支持

### CSS 框架
- **Tailwind CSS 3.4+**
  - 实用优先的设计理念
  - 高度可定制
  - 小巧的生产包体积
  - 响应式设计支持

### 状态管理
- **Pinia**
  - Vue 3 官方推荐
  - 更好的 TypeScript 支持
  - 模块化设计
  - DevTools 支持

### 路由管理
- **Vue Router 4**
  - 官方路由解决方案
  - 支持嵌套路由
  - 路由守卫
  - 懒加载支持

## 🗂️ 项目目录结构详解

```
src/
├── api/                      # API 接口层
│   ├── index.ts             # 统一请求封装
│   ├── backup/              # 备份相关 API
│   ├── banconfig/           # 黑白名单 API
│   ├── censor/              # 敏感词过滤 API
│   ├── configs/             # 配置管理 API
│   ├── deck/                # 牌堆管理 API
│   ├── dice/                # 骰子核心 API
│   ├── group/               # 群组管理 API
│   ├── helpdoc/             # 帮助文档 API
│   ├── im_connections/      # IM 连接 API
│   ├── js/                  # JS 扩展 API
│   ├── public_dice/         # 公骰设置 API
│   ├── resource/            # 资源管理 API
│   ├── signin/              # 登录认证 API
│   ├── story/               # 跑团日志 API
│   └── utils/               # 工具 API
│
├── components/              # 组件目录
│   ├── Menu.vue            # 菜单组件
│   ├── PageAbout.vue       # 关于页面
│   ├── PageConnectInfoItems.vue  # 连接信息
│   ├── PageCustomText.vue  # 自定义文案
│   ├── PageHome.vue        # 首页
│   ├── customText/         # 自定义文案相关组件
│   ├── misc/               # 综合设置相关组件
│   │   ├── PageMiscAdvancedSettings.vue
│   │   ├── PageMiscBackup.vue
│   │   ├── PageMiscBan.vue
│   │   ├── PageMiscDicePublic.vue
│   │   ├── PageMiscGroup.vue
│   │   └── PageMiscSettings.vue
│   ├── mod/                # 扩展功能相关组件
│   │   ├── PageCensor.vue
│   │   ├── PageCustomReply.vue
│   │   ├── PageHelpDoc.vue
│   │   ├── PageJs.vue
│   │   ├── PageMiscDeck.vue
│   │   └── PageStory.vue
│   ├── tool/               # 工具相关组件
│   │   ├── PageResource.vue
│   │   └── PageTest.vue
│   └── utils/              # 工具组件
│       ├── custom-reply-conditions.vue
│       ├── diff-viewer.vue
│       ├── foldable-card.vue
│       ├── nested.vue
│       └── resource-render.vue
│
├── router/                 # 路由配置
│   └── index.ts           # 路由定义
│
├── store/                  # 状态管理
│   └── index.ts           # Pinia store
│
├── styles/                 # 样式文件
│   ├── index.css          # 全局样式
│   └── element/           # Element Plus 样式覆盖
│
├── App.vue                # 根组件
├── Main.vue               # 主布局组件
├── main.ts                # 应用入口
├── backend.ts             # 后端请求配置
├── type.d.ts              # 类型定义
└── utils.ts               # 工具函数
```

## 🔄 数据流架构

### 单向数据流

```
View (组件) 
    ↓ 用户交互
Action (方法调用)
    ↓ 调用 API
API Layer (接口层)
    ↓ HTTP 请求
Backend (后端服务)
    ↓ 响应数据
Store (状态更新)
    ↓ 响应式更新
View (视图更新)
```

### 状态管理策略

1. **全局状态** - 使用 Pinia Store
   - 用户认证信息
   - 骰子服务器信息
   - 连接状态
   - 配置信息

2. **组件状态** - 使用 Composition API
   - 表单数据
   - UI 状态
   - 临时数据

3. **持久化状态** - 使用 LocalStorage
   - Token 存储
   - 用户偏好设置
   - 缓存数据

## 🔌 API 设计

### RESTful API 规范

```typescript
// API 基础结构
interface ApiResponse<T> {
  result: boolean;
  data?: T;
  err?: string;
  message?: string;
}

// 请求拦截器
http.interceptors.request.use(config => {
  // 添加认证 token
  const store = useStore();
  if (store.token) {
    config.headers.token = store.token;
  }
  return config;
});

// 响应拦截器
http.interceptors.response.use(
  response => response.data,
  error => {
    // 统一错误处理
    if (error.response?.status === 401) {
      // 认证失败处理
    }
    return Promise.reject(error);
  }
);
```

### API 模块化设计

每个功能模块都有独立的 API 文件：

```typescript
// api/dice/index.ts
export const getDiceConfig = () => 
  request<DiceConfig>('/dice/config');

export const setDiceConfig = (data: DiceConfig) => 
  request('/dice/config', {
    method: 'POST',
    data
  });
```

## 🎨 组件设计原则

### 组件分层

1. **页面组件（Pages）**
   - 路由级别的组件
   - 负责数据获取和业务逻辑
   - 组合多个功能组件

2. **功能组件（Features）**
   - 具体功能实现
   - 可复用的业务组件
   - 包含业务逻辑

3. **基础组件（Base）**
   - 纯展示组件
   - 高度可复用
   - 无业务逻辑

### 组件通信

```
父子组件通信：
- Props（父→子）
- Emits（子→父）

跨组件通信：
- Pinia Store（全局状态）
- Provide/Inject（依赖注入）
- Event Bus（事件总线）
```

## 🚀 性能优化策略

### 构建优化

```javascript
// vite.config.ts
build: {
  rollupOptions: {
    output: {
      manualChunks: {
        base: ['vue', 'pinia', 'vue-router'],
        codemirror: ['codemirror', '@codemirror/lang-javascript'],
        common: ['element-plus', 'lodash-es'],
        utils: ['@vueuse/core', 'axios', 'dayjs']
      }
    }
  }
}
```

### 运行时优化

1. **路由懒加载**
   ```typescript
   const PageHome = () => import('./components/PageHome.vue')
   ```

2. **组件懒加载**
   ```vue
   <component :is="defineAsyncComponent(() => import('./Heavy.vue'))" />
   ```

3. **虚拟滚动**
   - 大列表数据使用虚拟滚动
   - 减少 DOM 节点数量

4. **防抖节流**
   - 搜索输入防抖
   - 滚动事件节流

## 🔒 安全架构

### 前端安全措施

1. **XSS 防护**
   - 使用 Vue 的自动转义
   - 避免使用 v-html
   - 输入验证和过滤

2. **CSRF 防护**
   - Token 验证
   - SameSite Cookie

3. **密码安全**
   ```typescript
   // 使用 AsmCrypto.js 进行密码哈希
   import { passwordHash } from './utils';
   const hash = await passwordHash(salt, password);
   ```

4. **敏感信息保护**
   - Token 存储在内存或 SessionStorage
   - 避免在 URL 中传递敏感信息

## 📱 响应式设计

### 断点设计

```css
/* Tailwind CSS 断点 */
sm: 640px   /* 平板竖屏 */
md: 768px   /* 平板横屏 */
lg: 1024px  /* 桌面 */
xl: 1280px  /* 大屏幕 */
2xl: 1536px /* 超大屏幕 */
```

### 移动端适配

1. **触摸优化**
   - 增大可点击区域
   - 支持手势操作

2. **布局适配**
   - 弹性布局
   - 响应式网格

3. **性能优化**
   - 图片懒加载
   - 减少动画效果

## 🔄 部署架构

### 生产环境构建

```bash
# 构建命令
pnpm run build

# 输出目录结构
dist/
├── index.html
├── assets/
│   ├── js/
│   ├── css/
│   └── img/
└── favicon.svg
```

### 部署方案

1. **静态资源服务**
   - Nginx/Apache 静态服务
   - CDN 加速

2. **反向代理配置**
   ```nginx
   location /sd-api {
     proxy_pass http://backend-server;
     proxy_set_header Host $host;
     proxy_set_header X-Real-IP $remote_addr;
   }
   ```

## 📊 监控与日志

### 错误监控

```typescript
// 全局错误处理
app.config.errorHandler = (err, vm, info) => {
  console.error('Global error:', err, info);
  // 上报错误到监控系统
};
```

### 性能监控

- 首屏加载时间
- 路由切换性能
- API 响应时间
- 资源加载监控

## 🔮 未来架构规划

1. **微前端架构**
   - 模块独立部署
   - 技术栈解耦

2. **服务端渲染（SSR）**
   - 改善 SEO
   - 提升首屏性能

3. **PWA 支持**
   - 离线访问
   - 应用安装

4. **国际化（i18n）**
   - 多语言支持
   - 本地化适配

---

> 📚 **参考资料**：
> - [Vue 3 官方文档](https://cn.vuejs.org/)
> - [Vite 官方文档](https://cn.vitejs.dev/)
> - [Element Plus 文档](https://element-plus.org/zh-CN/)
> - [Pinia 文档](https://pinia.vuejs.org/zh/)