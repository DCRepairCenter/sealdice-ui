# 开发环境搭建指南

## 📋 前置要求

在开始之前，请确保您的开发环境满足以下要求：

### 系统要求
- **操作系统**：Windows 10+、macOS 10.15+、Linux（Ubuntu 20.04+ 推荐）
- **内存**：至少 4GB RAM（推荐 8GB+）
- **硬盘空间**：至少 2GB 可用空间

### 软件要求

| 软件 | 最低版本 | 推荐版本 | 说明 |
|------|---------|---------|------|
| Node.js | 16.0.0 | 18.0.0+ | JavaScript 运行环境 |
| pnpm | 7.0.0 | 8.0.0+ | 包管理器（推荐） |
| Git | 2.0.0 | 最新版 | 版本控制工具 |
| VS Code | - | 最新版 | 推荐的代码编辑器 |

## 🚀 快速开始

### 1. 安装 Node.js

访问 [Node.js 官网](https://nodejs.org/) 下载并安装 LTS 版本。

验证安装：
```bash
node --version
# 输出: v18.x.x

npm --version
# 输出: 9.x.x
```

### 2. 安装 pnpm

pnpm 是推荐的包管理器，提供更快的安装速度和更小的磁盘占用。

```bash
# 使用 npm 安装
npm install -g pnpm

# 或使用 corepack（Node.js 16.13+ 自带）
corepack enable
corepack prepare pnpm@latest --activate

# 验证安装
pnpm --version
```

### 3. 克隆项目

```bash
# 使用 HTTPS
git clone https://github.com/sealdice/sealdice-ui.git

# 或使用 SSH
git clone git@github.com:sealdice/sealdice-ui.git

# 进入项目目录
cd sealdice-ui
```

### 4. 安装依赖

```bash
# 使用 pnpm 安装（推荐）
pnpm install

# 或使用 npm
npm install

# 或使用 yarn
yarn install
```

### 5. 配置开发环境

创建 `.env` 文件用于本地开发配置：

```bash
# 复制环境变量模板
cp .env.example .env
```

编辑 `.env` 文件：

```env
# 后端 API 地址
VITE_APP_APIURL=http://localhost:3211

# 其他配置项...
```

### 6. 启动开发服务器

```bash
# 启动开发服务器
pnpm run dev

# 或在所有网络接口上启动（可以通过局域网访问）
pnpm run dev-host
```

开发服务器启动后，在浏览器中访问：
- 本地访问：`http://localhost:5173`
- 局域网访问：`http://[你的IP]:5173`

## 🛠️ 开发工具配置

### VS Code 推荐配置

#### 必装扩展

创建 `.vscode/extensions.json`：

```json
{
  "recommendations": [
    "Vue.volar",
    "Vue.vscode-typescript-vue-plugin",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "bradlc.vscode-tailwindcss",
    "antfu.iconify",
    "lokalise.i18n-ally",
    "mikestead.dotenv"
  ]
}
```

#### 工作区设置

创建 `.vscode/settings.json`：

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "vue"
  ],
  "typescript.tsdk": "node_modules/typescript/lib",
  "tailwindCSS.experimental.classRegex": [
    ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"]
  ]
}
```

### 浏览器扩展

推荐安装以下浏览器扩展以提升开发体验：

- **Vue.js devtools** - Vue 开发者工具
- **Redux DevTools** - 用于 Pinia 状态管理调试
- **Lighthouse** - 性能分析工具

## 📝 开发脚本说明

package.json 中定义的常用脚本：

| 命令 | 说明 |
|------|------|
| `pnpm run dev` | 启动开发服务器 |
| `pnpm run dev-host` | 启动开发服务器（所有网络接口） |
| `pnpm run build` | 构建生产版本 |
| `pnpm run preview` | 预览生产构建 |
| `pnpm run type-check` | TypeScript 类型检查 |
| `pnpm run lint` | ESLint 代码检查 |
| `pnpm run lint:fix` | ESLint 自动修复 |
| `pnpm run check` | 运行所有检查（类型+lint） |

## 🔧 项目配置详解

### Vite 配置

`vite.config.ts` 主要配置项：

```typescript
export default defineConfig({
  base: './',                    // 基础路径
  server: {
    proxy: {
      '/sd-api': {              // API 代理配置
        target: 'http://localhost:3211',
        changeOrigin: true
      }
    }
  },
  plugins: [
    vue(),                      // Vue 3 支持
    AutoImport(),               // 自动导入
    Components(),               // 组件自动注册
    Icons(),                    // 图标支持
    legacy()                    // 旧版浏览器兼容
  ]
})
```

### TypeScript 配置

`tsconfig.json` 配置说明：

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "lib": ["ES2020", "DOM"],
    "jsx": "preserve",
    "strict": true,
    "paths": {
      "~/*": ["./src/*"]        // 路径别名
    }
  }
}
```

### ESLint 配置

`eslint.config.mjs` 主要规则：

```javascript
export default [
  {
    rules: {
      'vue/multi-word-component-names': 'off',
      '@typescript-eslint/no-explicit-any': 'warn',
      'prettier/prettier': 'error'
    }
  }
]
```

## 🔄 连接后端服务

### 本地后端开发

1. 克隆并启动 SealDice Core：

```bash
# 克隆后端仓库
git clone https://github.com/sealdice/sealdice-core.git
cd sealdice-core

# 编译并运行
go build
./sealdice-core
```

2. 配置前端连接：

修改 `.env` 文件：
```env
VITE_APP_APIURL=http://localhost:3211
```

### 连接远程后端

如果要连接远程的 SealDice 实例：

```env
VITE_APP_APIURL=https://your-sealdice-server.com
```

## 🐛 常见问题解决

### 1. 依赖安装失败

**问题**：`pnpm install` 失败

**解决方案**：
```bash
# 清理缓存
pnpm store prune

# 删除 node_modules 和锁文件
rm -rf node_modules pnpm-lock.yaml

# 重新安装
pnpm install
```

### 2. 端口被占用

**问题**：端口 5173 已被占用

**解决方案**：

修改 `vite.config.ts`：
```typescript
server: {
  port: 3000  // 使用其他端口
}
```

### 3. 网络代理问题

**问题**：在公司网络环境下无法下载依赖

**解决方案**：
```bash
# 设置 npm 代理
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080

# 设置 pnpm 代理
pnpm config set proxy http://proxy.company.com:8080
pnpm config set https-proxy http://proxy.company.com:8080
```

### 4. TypeScript 错误

**问题**：VS Code 显示大量 TypeScript 错误

**解决方案**：
1. 确保安装了 Volar 扩展
2. 禁用 Vetur 扩展（如果安装了）
3. 重启 VS Code
4. 运行 `pnpm run type-check` 验证

### 5. 热更新不生效

**问题**：修改代码后页面不自动刷新

**解决方案**：
1. 检查是否在 WSL 中开发（可能需要额外配置）
2. 尝试使用 `pnpm run dev-host`
3. 检查防火墙设置

## 📚 开发资源

### 官方文档
- [Vue 3 文档](https://cn.vuejs.org/)
- [Vite 文档](https://cn.vitejs.dev/)
- [Element Plus 组件库](https://element-plus.org/zh-CN/)
- [Pinia 状态管理](https://pinia.vuejs.org/zh/)
- [Vue Router](https://router.vuejs.org/zh/)

### 学习资源
- [Vue 3 Composition API 教程](https://cn.vuejs.org/guide/extras/composition-api-faq.html)
- [TypeScript 手册](https://www.typescriptlang.org/zh/docs/)
- [Tailwind CSS 文档](https://tailwindcss.com/docs)

### 开发工具
- [Vue DevTools](https://devtools.vuejs.org/)
- [Vite 插件社区](https://github.com/vitejs/awesome-vite)
- [VS Code Vue 工具](https://marketplace.visualstudio.com/items?itemName=Vue.volar)

## 💡 开发建议

### 代码风格
1. 使用 Composition API 而非 Options API
2. 优先使用 `<script setup>` 语法
3. 组件命名采用 PascalCase
4. 文件命名采用 kebab-case

### 性能优化
1. 使用 `defineAsyncComponent` 进行组件懒加载
2. 合理使用 `v-show` 和 `v-if`
3. 大列表使用虚拟滚动
4. 图片资源使用懒加载

### Git 工作流
1. 创建功能分支进行开发
2. 提交信息遵循 Conventional Commits
3. 定期同步上游更新
4. 提交前运行 `pnpm run check`

## 🤝 获取帮助

如果在开发过程中遇到问题：

1. 查看项目 [Issues](https://github.com/sealdice/sealdice-ui/issues)
2. 查阅 [海豹手册](https://docs.sealdice.com/)
3. 加入官方交流群
4. 在 GitHub 上提交 Issue

---

> 💪 **提示**：保持开发环境的更新，定期运行 `pnpm update` 更新依赖，确保使用最新的功能和修复。