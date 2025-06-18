# Docker 容器化部署指南

Docker 是一个开源的容器化平台，它可以帮助开发者更轻松地构建、部署和运行应用程序。本文将介绍如何使用 Docker 进行应用程序的容器化部署。

## 什么是 Docker？

Docker 是一个轻量级的虚拟化技术，它使用容器来打包应用程序及其依赖项。与传统虚拟机不同，Docker 容器共享主机操作系统的内核，因此更加高效。

### Docker 的核心概念

- **镜像 (Image)**: 只读模板，用于创建容器
- **容器 (Container)**: 镜像的运行实例
- **Dockerfile**: 用于构建镜像的文本文件
- **Registry**: 存储和分发镜像的服务

## 创建 Dockerfile

Dockerfile 是构建 Docker 镜像的配置文件。以下是一个 Node.js 应用的示例：

```dockerfile
# 使用官方 Node.js 镜像作为基础
FROM node:18-alpine

# 设置工作目录
WORKDIR /app

# 复制 package.json 和 package-lock.json
COPY package*.json ./

# 安装依赖
RUN npm ci --only=production

# 复制应用程序代码
COPY . .

# 暴露端口
EXPOSE 3000

# 设置启动命令
CMD ["npm", "start"]
```

## 构建和运行容器

### 构建镜像

```bash
# 构建镜像
docker build -t my-app:latest .

# 查看镜像
docker images
```

### 运行容器

```bash
# 运行容器
docker run -d -p 3000:3000 --name my-app-container my-app:latest

# 查看运行中的容器
docker ps

# 查看容器日志
docker logs my-app-container
```

## Docker Compose

对于多容器应用，使用 Docker Compose 可以简化部署过程：

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DB_HOST=db
    depends_on:
      - db
    volumes:
      - ./logs:/app/logs

  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:6-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

### 使用 Docker Compose

```bash
# 启动所有服务
docker-compose up -d

# 查看服务状态
docker-compose ps

# 查看日志
docker-compose logs app

# 停止所有服务
docker-compose down
```

## 最佳实践

### 1. 多阶段构建

使用多阶段构建可以减小镜像大小：

```dockerfile
# 构建阶段
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# 生产阶段
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 2. 使用 .dockerignore

创建 `.dockerignore` 文件来排除不必要的文件：

```
node_modules
npm-debug.log
.git
.gitignore
README.md
.env
coverage
.nyc_output
```

### 3. 健康检查

添加健康检查来监控容器状态：

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1
```

### 4. 安全考虑

- 使用非 root 用户运行应用
- 定期更新基础镜像
- 扫描镜像漏洞

```dockerfile
# 创建非 root 用户
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# 切换到非 root 用户
USER nextjs
```

## 部署到生产环境

### 使用 Docker Swarm

```bash
# 初始化 Swarm 集群
docker swarm init

# 部署服务
docker stack deploy -c docker-compose.yml myapp

# 查看服务状态
docker service ls
```

### 使用 Kubernetes

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app:latest
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: "production"
```

## 监控和日志

### 日志管理

```bash
# 查看容器日志
docker logs -f my-app-container

# 使用日志驱动
docker run --log-driver=syslog my-app:latest
```

### 监控工具

- **Prometheus + Grafana**: 监控和可视化
- **ELK Stack**: 日志收集和分析
- **cAdvisor**: 容器资源监控

## 总结

Docker 容器化技术可以显著简化应用的部署和管理。通过遵循最佳实践，你可以构建高效、安全、可扩展的容器化应用。

记住以下要点：
- 保持镜像尽可能小
- 使用多阶段构建优化构建过程
- 实施适当的安全措施
- 建立完善的监控和日志系统

---

*祝你在容器化的道路上一帆风顺！*
