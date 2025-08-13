# API 接口文档

## 📡 概述

SealDice UI 通过 RESTful API 与后端服务进行通信。所有 API 请求都通过统一的请求封装层处理，支持认证、错误处理和重试机制。

### 基础配置

- **API 前缀**：`/sd-api`
- **请求格式**：JSON
- **响应格式**：JSON
- **认证方式**：Token（存储在请求头）

### 统一响应格式

```typescript
interface ApiResponse<T = any> {
  result: boolean;      // 请求是否成功
  data?: T;            // 响应数据
  err?: string;        // 错误信息
  message?: string;    // 提示信息
}
```

## 🔐 认证接口

### 获取盐值

```http
GET /sd-api/auth/salt
```

**响应示例**：
```json
{
  "result": true,
  "salt": "abc123..."
}
```

### 登录

```http
POST /sd-api/signin
```

**请求参数**：
```json
{
  "password": "hashed_password"
}
```

**响应示例**：
```json
{
  "result": true,
  "token": "jwt_token_here"
}
```

## 🎲 骰子核心接口

### 获取基础信息

```http
GET /sd-api/dice/baseinfo
```

**响应字段**：
| 字段 | 类型 | 说明 |
|------|------|------|
| appChannel | string | 应用渠道（stable/beta） |
| version | string | 当前版本 |
| versionSimple | string | 简化版本号 |
| versionNew | string | 最新版本 |
| versionCode | number | 版本代码 |
| memoryUsedSys | number | 系统内存使用 |
| uptime | number | 运行时间 |
| OS | string | 操作系统 |
| arch | string | 系统架构 |
| containerMode | boolean | 是否容器模式 |

### 获取配置

```http
GET /sd-api/dice/config
```

**响应示例**：
```json
{
  "result": true,
  "data": {
    "helpDocEngineType": 0,
    "helpDoc": {},
    "masterList": [],
    "blackList": [],
    "whiteList": [],
    "commandPrefix": [".", "。"],
    "diceMasters": ["UI:1001"],
    "replyDebugMode": false
  }
}
```

### 更新配置

```http
POST /sd-api/dice/config
```

**请求参数**：
```json
{
  "helpDocEngineType": 0,
  "commandPrefix": [".", "。"],
  "diceMasters": ["UI:1001"],
  "replyDebugMode": false
}
```

## 🔗 IM 连接管理

### 获取连接列表

```http
GET /sd-api/im_connections/list
```

**响应字段**：
| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | 连接 ID |
| state | number | 连接状态 |
| platform | string | 平台类型 |
| nickname | string | 昵称 |
| userId | number | 用户 ID |
| groupNum | number | 群组数量 |
| enable | boolean | 是否启用 |

### 添加 QQ 连接（Gocq）

```http
POST /sd-api/im_connections/gocq/add
```

**请求参数**：
```json
{
  "account": "QQ号",
  "password": "密码",
  "protocol": 1,
  "appVersion": "8.9.63",
  "useSignServer": false,
  "signServerConfig": {}
}
```

### 添加 Discord 连接

```http
POST /sd-api/im_connections/discord/add
```

**请求参数**：
```json
{
  "token": "bot_token",
  "proxyURL": "",
  "reverseProxyUrl": "",
  "reverseProxyCDNUrl": ""
}
```

### 删除连接

```http
DELETE /sd-api/im_connections/{id}
```

### 启用/禁用连接

```http
POST /sd-api/im_connections/{id}/enable
POST /sd-api/im_connections/{id}/disable
```

## 📝 自定义文案

### 获取文案列表

```http
GET /sd-api/configs/custom_text
```

**响应示例**：
```json
{
  "result": true,
  "texts": {
    "核心": {
      "骰子名字": [["海豹核心"]],
      "骰子帮助文本": [["使用.help查看帮助"]]
    },
    "COC": {
      "检定_大成功": [["大成功！"]],
      "检定_极难成功": [["极难成功"]]
    }
  },
  "helpInfo": {}
}
```

### 保存文案

```http
POST /sd-api/configs/custom_text/{category}
```

**请求参数**：
```json
{
  "骰子名字": [["我的骰子"]],
  "骰子帮助文本": [["自定义帮助文本"]]
}
```

## 🛡️ 黑白名单管理

### 获取黑白名单配置

```http
GET /sd-api/banconfig/list
```

**响应示例**：
```json
{
  "result": true,
  "blackList": [
    {
      "id": "QQ:123456",
      "name": "用户名",
      "reason": "违规原因",
      "bannedAt": 1234567890
    }
  ],
  "whiteList": []
}
```

### 添加黑名单

```http
POST /sd-api/banconfig/ban/add
```

**请求参数**：
```json
{
  "id": "QQ:123456",
  "reason": "违规原因",
  "duration": 86400
}
```

### 移除黑名单

```http
POST /sd-api/banconfig/ban/remove
```

**请求参数**：
```json
{
  "id": "QQ:123456"
}
```

## 📚 牌堆管理

### 获取牌堆列表

```http
GET /sd-api/deck/list
```

**响应示例**：
```json
{
  "result": true,
  "decks": [
    {
      "name": "牌堆名称",
      "author": "作者",
      "version": "1.0",
      "commands": ["draw"],
      "format": {},
      "meta": {}
    }
  ]
}
```

### 上传牌堆

```http
POST /sd-api/deck/upload
```

**请求参数**：
```json
{
  "name": "新牌堆",
  "content": "牌堆内容（YAML/JSON格式）"
}
```

### 删除牌堆

```http
DELETE /sd-api/deck/{name}
```

## 📊 群组管理

### 获取群组列表

```http
GET /sd-api/group/list
```

**响应字段**：
| 字段 | 类型 | 说明 |
|------|------|------|
| groupId | string | 群组 ID |
| groupName | string | 群组名称 |
| platform | string | 平台 |
| memberCount | number | 成员数量 |
| botOn | boolean | 机器人是否开启 |
| lastActive | number | 最后活跃时间 |

### 群组设置

```http
POST /sd-api/group/{groupId}/config
```

**请求参数**：
```json
{
  "botOn": true,
  "logOn": true,
  "showGroupWelcome": true,
  "groupWelcomeText": "欢迎加入群组"
}
```

## 📝 JS 扩展

### 获取脚本列表

```http
GET /sd-api/js/list
```

**响应示例**：
```json
{
  "result": true,
  "scripts": [
    {
      "name": "example.js",
      "enabled": true,
      "version": "1.0.0",
      "author": "作者",
      "description": "脚本描述"
    }
  ]
}
```

### 上传脚本

```http
POST /sd-api/js/upload
```

**请求参数**：
```json
{
  "name": "script.js",
  "content": "// JavaScript 代码"
}
```

### 启用/禁用脚本

```http
POST /sd-api/js/{name}/enable
POST /sd-api/js/{name}/disable
```

## 📜 跑团日志

### 获取日志列表

```http
GET /sd-api/story/list
```

**响应字段**：
| 字段 | 类型 | 说明 |
|------|------|------|
| logId | string | 日志 ID |
| groupId | string | 群组 ID |
| groupName | string | 群组名称 |
| startTime | number | 开始时间 |
| endTime | number | 结束时间 |
| messageCount | number | 消息数量 |

### 导出日志

```http
GET /sd-api/story/export/{logId}
```

**查询参数**：
- `format`: 导出格式（txt/html/json）

### 删除日志

```http
DELETE /sd-api/story/{logId}
```

## 💾 备份管理

### 获取备份列表

```http
GET /sd-api/backup/list
```

**响应示例**：
```json
{
  "result": true,
  "backups": [
    {
      "name": "backup_20250813.zip",
      "size": 1024000,
      "createdAt": 1234567890,
      "type": "auto"
    }
  ]
}
```

### 创建备份

```http
POST /sd-api/backup/create
```

**请求参数**：
```json
{
  "name": "manual_backup",
  "includeImages": true,
  "includeExtensions": true
}
```

### 恢复备份

```http
POST /sd-api/backup/restore
```

**请求参数**：
```json
{
  "filename": "backup_20250813.zip"
}
```

### 下载备份

```http
GET /sd-api/backup/download/{filename}
```

## 🚫 拦截管理

### 获取敏感词列表

```http
GET /sd-api/censor/words
```

**响应示例**：
```json
{
  "result": true,
  "words": [
    {
      "id": 1,
      "word": "敏感词",
      "level": 1,
      "action": "warn"
    }
  ]
}
```

### 添加敏感词

```http
POST /sd-api/censor/words/add
```

**请求参数**：
```json
{
  "word": "新敏感词",
  "level": 1,
  "action": "warn"
}
```

### 获取拦截日志

```http
GET /sd-api/censor/logs
```

**查询参数**：
- `page`: 页码
- `pageSize`: 每页数量
- `startTime`: 开始时间
- `endTime`: 结束时间

## 🛠️ 工具接口

### 指令测试

```http
POST /sd-api/tool/test
```

**请求参数**：
```json
{
  "command": ".rd 1d100",
  "userId": "UI:1001",
  "groupId": "UI:1002",
  "mode": "private"
}
```

**响应示例**：
```json
{
  "result": true,
  "response": "* UI:1001 掷骰: 1d100 = 42"
}

### 网络健康检查

```http
GET /sd-api/utils/network_health
```

**响应示例**：
```json
{
  "result": true,
  "total": 4,
  "ok": ["seal", "sign", "google", "github"],
  "timestamp": 1234567890
}
```

### 获取资源列表

```http
GET /sd-api/resource/list
```

**查询参数**：
- `type`: 资源类型（image/audio/video）
- `page`: 页码
- `pageSize`: 每页数量

**响应示例**：
```json
{
  "result": true,
  "resources": [
    {
      "name": "example.jpg",
      "type": "image",
      "size": 102400,
      "path": "/images/example.jpg"
    }
  ],
  "total": 100
}
```

## 🔧 高级配置

### 获取高级配置

```http
GET /sd-api/advanced/config
```

**响应示例**：
```json
{
  "result": true,
  "config": {
    "show": true,
    "enable": true,
    "debugMode": false,
    "experimentalFeatures": []
  }
}
```

### 更新高级配置

```http
POST /sd-api/advanced/config
```

## 📈 日志接口

### 获取系统日志

```http
GET /sd-api/logs/fetch
```

**响应示例**：
```json
{
  "result": true,
  "logs": [
    {
      "level": "info",
      "ts": 1234567890,
      "caller": "main.go:123",
      "msg": "系统启动成功"
    }
  ]
}
```

### 清空日志

```http
POST /sd-api/logs/clear
```

## 🔑 错误码说明

| 错误码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 400 | 请求参数错误 |
| 401 | 未授权/Token 失效 |
| 403 | 权限不足 |
| 404 | 资源不存在 |
| 500 | 服务器内部错误 |
| 503 | 服务不可用 |

## 📚 使用示例

### 在项目中调用 API

```typescript
// api/dice/index.ts
import request from '../index';

// 获取骰子配置
export const getDiceConfig = () => {
  return request<DiceConfig>({
    url: '/dice/config',
    method: 'GET'
  });
};

// 更新骰子配置
export const setDiceConfig = (data: DiceConfig) => {
  return request({
    url: '/dice/config',
    method: 'POST',
    data
  });
};
```

### 在组件中使用

```vue
<script setup lang="ts">
import { getDiceConfig, setDiceConfig } from '~/api/dice';

const config = ref<DiceConfig>();

// 加载配置
onMounted(async () => {
  const res = await getDiceConfig();
  if (res.result) {
    config.value = res.data;
  }
});

// 保存配置
const saveConfig = async () => {
  const res = await setDiceConfig(config.value);
  if (res.result) {
    ElMessage.success('配置保存成功');
  }
};
</script>
```

---

> 📝 **注意**：本文档基于项目代码分析生成，实际 API 可能会随版本更新而变化。请以最新的后端 API 文档为准。