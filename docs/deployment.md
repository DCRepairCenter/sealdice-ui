# 部署指南

## 🚀 部署概述

SealDice UI 是一个纯静态的前端应用，可以部署到任何支持静态文件服务的环境中。本指南将介绍多种部署方案。

## 📋 部署前准备

### 环境要求

- Node.js 16.0.0+（用于构建）
- Web 服务器（Nginx/Apache/IIS 等）
- 后端服务（SealDice Core）已部署运行

### 构建生产版本

```bash
# 克隆项目
git clone https://github.com/sealdice/sealdice-ui.git
cd sealdice-ui

# 安装依赖
pnpm install

# 构建生产版本
pnpm run build
```

构建完成后，生产文件将生成在 `dist` 目录中。

## 🌐 部署方案

### 方案一：Nginx 部署（推荐）

#### 1. 安装 Nginx

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install nginx

# CentOS/RHEL
sudo yum install epel-release
sudo yum install nginx

# macOS
brew install nginx
```

#### 2. 配置 Nginx

创建配置文件 `/etc/nginx/sites-available/sealdice-ui`：

```nginx
server {
    listen 80;
    server_name your-domain.com;  # 替换为你的域名
    
    # 网站根目录
    root /var/www/sealdice-ui;
    index index.html;
    
    # 开启 gzip 压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
    
    # 路由处理（支持 Vue Router History 模式）
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # API 代理
    location /sd-api {
        proxy_pass http://localhost:3211;  # 后端地址
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket 支持
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
    
    # 静态资源缓存
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

#### 3. 部署文件

```bash
# 复制构建文件到网站目录
sudo cp -r dist/* /var/www/sealdice-ui/

# 设置权限
sudo chown -R www-data:www-data /var/www/sealdice-ui
sudo chmod -R 755 /var/www/sealdice-ui
```

#### 4. 启用配置并重启 Nginx

```bash
# 创建符号链接
sudo ln -s /etc/nginx/sites-available/sealdice-ui /etc/nginx/sites-enabled/

# 测试配置
sudo nginx -t

# 重启 Nginx
sudo systemctl restart nginx
```

### 方案二：Apache 部署

#### 1. 安装 Apache

```bash
# Ubuntu/Debian
sudo apt install apache2

# CentOS/RHEL
sudo yum install httpd
```

#### 2. 配置 Apache

创建配置文件 `/etc/apache2/sites-available/sealdice-ui.conf`：

```apache
<VirtualHost *:80>
    ServerName your-domain.com
    DocumentRoot /var/www/sealdice-ui
    
    # 启用必要的模块
    # sudo a2enmod rewrite proxy proxy_http headers
    
    <Directory /var/www/sealdice-ui>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    # API 代理
    ProxyPass /sd-api http://localhost:3211/sd-api
    ProxyPassReverse /sd-api http://localhost:3211/sd-api
    
    # 路由重写（支持 Vue Router）
    RewriteEngine On
    RewriteBase /
    RewriteRule ^index\.html$ - [L]
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule . /index.html [L]
    
    # 开启压缩
    <IfModule mod_deflate.c>
        AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript application/javascript application/json
    </IfModule>
</VirtualHost>
```

#### 3. 启用站点

```bash
# 启用站点
sudo a2ensite sealdice-ui.conf

# 启用必要的模块
sudo a2enmod rewrite proxy proxy_http headers

# 重启 Apache
sudo systemctl restart apache2
```

### 方案三：Docker 部署

#### 1. 创建 Dockerfile

```dockerfile
# 构建阶段
FROM node:18-alpine as builder

WORKDIR /app

# 复制依赖文件
COPY package.json pnpm-lock.yaml ./

# 安装 pnpm
RUN npm install -g pnpm

# 安装依赖
RUN pnpm install --frozen-lockfile

# 复制源代码
COPY . .

# 构建应用
RUN pnpm run build

# 运行阶段
FROM nginx:alpine

# 复制构建产物
COPY --from=builder /app/dist /usr/share/nginx/html

# 复制 nginx 配置
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

#### 2. 创建 nginx.conf

```nginx
server {
    listen 80;
    server_name localhost;
    
    root /usr/share/nginx/html;
    index index.html;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    location /sd-api {
        proxy_pass http://sealdice-core:3211;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

#### 3. 构建和运行

```bash
# 构建镜像
docker build -t sealdice-ui .

# 运行容器
docker run -d \
  --name sealdice-ui \
  -p 80:80 \
  --link sealdice-core \
  sealdice-ui
```

### 方案四：Docker Compose 部署

创建 `docker-compose.yml`：

```yaml
version: '3.8'

services:
  sealdice-ui:
    build: .
    ports:
      - "80:80"
    depends_on:
      - sealdice-core
    environment:
      - API_URL=http://sealdice-core:3211
    networks:
      - sealdice-network
    restart: unless-stopped

  sealdice-core:
    image: sealdice/sealdice-core:latest
    ports:
      - "3211:3211"
    volumes:
      - ./data:/app/data
    networks:
      - sealdice-network
    restart: unless-stopped

networks:
  sealdice-network:
    driver: bridge
```

启动服务：

```bash
docker-compose up -d
```

### 方案五：云平台部署

#### Vercel 部署

1. Fork 项目到你的 GitHub
2. 登录 [Vercel](https://vercel.com)
3. 导入 GitHub 项目
4. 配置环境变量：
   ```
   VITE_APP_APIURL=https://your-backend-api.com
   ```
5. 点击部署

#### Netlify 部署

1. Fork 项目到你的 GitHub
2. 登录 [Netlify](https://netlify.com)
3. 创建新站点，选择 GitHub 项目
4. 配置构建命令：
   - Build command: `pnpm run build`
   - Publish directory: `dist`
5. 添加环境变量
6. 部署

#### GitHub Pages 部署

1. 修改 `vite.config.ts`：
   ```typescript
   export default defineConfig({
     base: '/sealdice-ui/',  // 你的仓库名
     // ...
   })
   ```

2. 创建 GitHub Actions 工作流 `.github/workflows/deploy.yml`：
   ```yaml
   name: Deploy to GitHub Pages

   on:
     push:
       branches: [main]

   jobs:
     deploy:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v3
         
         - uses: pnpm/action-setup@v2
           with:
             version: 8
             
         - uses: actions/setup-node@v3
           with:
             node-version: 18
             cache: 'pnpm'
             
         - run: pnpm install
         - run: pnpm run build
         
         - uses: peaceiris/actions-gh-pages@v3
           with:
             github_token: ${{ secrets.GITHUB_TOKEN }}
             publish_dir: ./dist
   ```

### 方案六：宝塔面板部署

1. **创建站点**
   - 登录宝塔面板
   - 添加站点，设置域名

2. **上传文件**
   - 将 `dist` 目录内容上传到网站根目录

3. **配置伪静态**
   ```nginx
   location / {
     try_files $uri $uri/ /index.html;
   }
   ```

4. **配置反向代理**
   - 添加反向代理
   - 代理目录：`/sd-api`
   - 目标 URL：`http://127.0.0.1:3211`

## 🔧 配置说明

### 环境变量配置

创建 `.env.production` 文件：

```env
# API 后端地址
VITE_APP_APIURL=https://api.your-domain.com

# 其他配置
VITE_APP_TITLE=SealDice UI
```

### 跨域配置

如果前后端部署在不同域名，需要在后端配置 CORS：

```go
// SealDice Core 配置
cors:
  allowed_origins:
    - https://ui.your-domain.com
  allowed_methods:
    - GET
    - POST
    - PUT
    - DELETE
  allowed_headers:
    - Content-Type
    - Authorization
```

## 🔒 安全配置

### HTTPS 配置（Let's Encrypt）

```bash
# 安装 Certbot
sudo apt install certbot python3-certbot-nginx

# 获取证书
sudo certbot --nginx -d your-domain.com

# 自动续期
sudo certbot renew --dry-run
```

### 安全头配置

在 Nginx 配置中添加：

```nginx
# 安全头
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;
```

## 📊 性能优化

### CDN 加速

1. **使用 Cloudflare**
   - 添加域名到 Cloudflare
   - 配置 DNS 指向服务器
   - 启用缓存和压缩

2. **使用国内 CDN**
   - 七牛云
   - 阿里云 CDN
   - 腾讯云 CDN

### 资源优化

```bash
# 构建时启用优化
pnpm run build

# 分析包大小
pnpm run build -- --report
```

## 🔍 部署验证

### 检查清单

- [ ] 网站可以正常访问
- [ ] 静态资源加载正常
- [ ] API 连接正常
- [ ] WebSocket 连接正常（如果使用）
- [ ] 路由跳转正常
- [ ] 移动端适配正常

### 常见问题

#### 1. 404 错误

**问题**：刷新页面出现 404

**解决**：配置服务器支持 SPA 路由
```nginx
location / {
  try_files $uri $uri/ /index.html;
}
```

#### 2. API 连接失败

**问题**：前端无法连接后端 API

**解决**：
- 检查 API 地址配置
- 检查防火墙设置
- 配置反向代理

#### 3. 跨域问题

**问题**：浏览器报跨域错误

**解决**：
- 在后端配置 CORS
- 或使用反向代理

#### 4. 白屏问题

**问题**：部署后页面白屏

**解决**：
- 检查控制台错误
- 确认资源路径正确
- 检查 base 配置

## 📝 部署后维护

### 更新部署

```bash
# 拉取最新代码
git pull

# 重新构建
pnpm install
pnpm run build

# 替换文件
sudo cp -r dist/* /var/www/sealdice-ui/

# 清理缓存（如果使用 CDN）
```

### 监控建议

- 使用 Nginx 访问日志监控
- 配置错误告警
- 定期检查更新
- 备份配置文件

## 🆘 获取帮助

如果在部署过程中遇到问题：

1. 查看 [常见问题](#常见问题)
2. 查看服务器错误日志
3. 在 GitHub 提交 Issue
4. 加入官方交流群

---

> 📌 **提示**：选择适合你的部署方案，生产环境建议使用 HTTPS 和 CDN 加速。记得定期备份和更新。