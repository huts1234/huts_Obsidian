

# Docker容器深度指南

---

## 一、Docker核心概念
### 1. Docker vs 虚拟机
| **维度**        | **Docker容器**                  | **虚拟机（VM）**               |
|-----------------|---------------------------------|-------------------------------|
| 虚拟化层级      | 操作系统级（共享宿主机内核）      | 硬件级（独立内核）              |
| 启动速度        | 秒级（毫秒级）                   | 分钟级                         |
| 资源占用        | 低（仅进程所需资源）              | 高（需分配固定CPU/内存）        |
| 隔离性          | 进程级隔离（较虚拟机弱）           | 完全隔离                        |
| 镜像体积        | 通常为MB级（如Alpine镜像仅5MB）   | GB级（完整操作系统）            |

### 2. 核心组件
- **镜像（Image）**：只读模板（如`nginx:alpine`），包含应用及其依赖  
- **容器（Container）**：镜像的运行实例（可读写层）  
- **仓库（Registry）**：镜像存储中心（默认Docker Hub，私有仓库如Harbor）  
- **Dockerfile**：定义镜像构建步骤的脚本文件  
- **Docker Compose**：通过YAML文件管理多容器应用  

---

## 二、Docker安装与配置
### 1. 安装Docker引擎
```bash
# Linux通用安装脚本
curl -fsSL https://get.docker.com | sudo sh

# 验证安装
sudo docker run hello-world
```

### 2. 配置镜像加速（国内）
```bash
# 创建配置文件
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
    "https://dockerproxy.com",
    "https://hub-mirror.c.163.com"
  ]
}
EOF

# 重启服务
sudo systemctl restart docker
```

---

## 三、Docker核心操作
### 1. 镜像管理
| **操作**               | **命令示例**                                 | **说明**                     |
|------------------------|---------------------------------------------|-----------------------------|
| 拉取镜像               | `docker pull nginx:alpine`                 | 指定标签下载镜像              |
| 列出镜像               | `docker images` 或 `docker image ls`        | 显示本地镜像列表              |
| 删除镜像               | `docker rmi <image_id>`                    | 清理未使用的镜像              |
| 构建镜像               | `docker build -t myapp:v1 .`               | 根据Dockerfile构建镜像        |

### 2. 容器生命周期
| **操作**               | **命令示例**                                 | **说明**                     |
|------------------------|---------------------------------------------|-----------------------------|
| 启动新容器             | `docker run -d --name mynginx -p 80:80 nginx` | -d后台运行，-p端口映射        |
| 列出运行中容器         | `docker ps`                                 | 添加`-a`显示所有容器           |
| 停止/启动容器          | `docker stop mynginx` 或 `docker start mynginx` | 强制停止用`docker kill`       |
| 进入容器终端           | `docker exec -it mynginx /bin/sh`          | 交互式执行命令（推荐`sh`或`bash`） |
| 查看容器日志           | `docker logs -f mynginx`                   | 实时跟踪日志输出              |
| 删除容器               | `docker rm mynginx`                        | 强制删除运行中容器加`-f`      |

---

## 四、Docker数据持久化
### 1. 数据卷（Volumes）
```bash
# 创建并挂载数据卷
docker run -d --name mysql \
  -v mysql_data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql:8.0
```
- **查看卷信息**：`docker volume inspect mysql_data`  

### 2. 绑定挂载（Bind Mounts）
```bash
# 挂载宿主机目录到容器
docker run -d --name myapp \
  -v /host/path:/container/path \
  myapp:v1
```

---

## 五、Docker网络模型
### 1. 网络模式
| **模式**       | **描述**                                  | **应用场景**               |
|----------------|------------------------------------------|--------------------------|
| bridge（默认）  | 创建虚拟网桥，容器通过NAT通信              | 单主机多容器隔离环境        |
| host           | 容器共享宿主机网络栈                       | 高性能需求（如网络监控工具）|
| none           | 无网络接口                                | 特殊安全场景               |
| overlay        | 跨主机容器通信（Swarm集群）                | 分布式应用                 |

### 2. 自定义网络
```bash
# 创建自定义网络
docker network create mynet

# 运行容器并加入网络
docker run -d --name web --network mynet nginx
docker run -d --name db --network mynet mysql
```

---

## 六、Docker Compose编排
### 1. 安装Docker Compose
```bash
# 下载二进制文件（替换版本号）
sudo curl -L "https://github.com/docker/compose/releases/download/v2.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 2. 编写`docker-compose.yml`
```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    networks:
      - mynet

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - mynet

volumes:
  mysql_data:

networks:
  mynet:
```

### 3. 常用命令
```bash
docker-compose up -d    # 启动服务
docker-compose down     # 停止并删除容器
docker-compose logs -f  # 查看日志
```

---

## 七、最佳实践与安全
### 1. 镜像优化
- **多阶段构建**：减少最终镜像体积  
  ```dockerfile
  FROM golang:1.20 AS builder
  WORKDIR /app
  COPY . .
  RUN go build -o myapp .

  FROM alpine:3.18
  COPY --from=builder /app/myapp /usr/local/bin/
  CMD ["myapp"]
  ```

### 2. 安全建议
- **非root用户运行**：  
  ```dockerfile
  FROM alpine
  RUN adduser -D myuser
  USER myuser
  ```
- **定期更新镜像**：`docker scan`检测漏洞  
- **限制资源**：  
  ```bash
  docker run -d --memory=512m --cpus=1 myapp
  ```

---

## 八、常见问题解决
### 1. 容器启动失败
- **查看日志**：`docker logs <container_id>`  
- **检查端口冲突**：`netstat -tuln \| grep 80`  

### 2. 网络不通
- **验证防火墙**：`ufw allow 80/tcp`  
- **检查DNS配置**：`docker run --dns 8.8.8.8 nginx`  

### 3. 数据卷权限问题
- **设置文件权限**：在Dockerfile中添加`chmod`命令  
  ```dockerfile
  RUN chmod 755 /app/data
  ```

---

Docker容器技术极大简化了应用部署与维护流程，结合Kubernetes等编排工具，可构建高效可靠的云原生架构。 🐳🚀