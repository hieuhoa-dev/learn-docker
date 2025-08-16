# Tài liệu học Docker từ cơ bản đến nâng cao

## Mục lục
1. [Docker là gì?](#docker-là-gì)
2. [Cài đặt Docker](#cài-đặt-docker)
3. [Các khái niệm cơ bản](#các-khái-niệm-cơ-bản)
4. [Làm việc với Images](#làm-việc-với-images)
5. [Làm việc với Containers](#làm-việc-với-containers)
6. [Dockerfile](#dockerfile)
7. [Docker Compose](#docker-compose)
8. [Volumes và Networks](#volumes-và-networks)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)

---

## Docker là gì?

Docker là một nền tảng containerization mở cho phép đóng gói ứng dụng và dependencies vào các containers nhẹ, di động. Containers này có thể chạy trên bất kỳ hệ thống nào có Docker được cài đặt.

### Lợi ích của Docker:
- **Tính nhất quán**: "Chạy được trên máy tôi" → "Chạy được ở mọi nơi"
- **Hiệu suất cao**: Chia sẻ kernel của host OS, nhẹ hơn VMs
- **Khả năng mở rộng**: Dễ dàng scale up/down
- **DevOps**: Tích hợp tốt với CI/CD pipelines
- **Microservices**: Phù hợp với kiến trúc microservices

### Docker vs Virtual Machines:
| Docker Containers | Virtual Machines |
|-------------------|------------------|
| Chia sẻ OS kernel | Mỗi VM có OS riêng |
| Khởi động nhanh (giây) | Khởi động chậm (phút) |
| Sử dụng ít tài nguyên | Sử dụng nhiều tài nguyên |
| Kích thước nhỏ (MB) | Kích thước lớn (GB) |

---

## Cài đặt Docker

### Ubuntu/Debian:
```bash
# Cập nhật package index
sudo apt update

# Cài đặt dependencies
sudo apt install apt-transport-https ca-certificates curl software-properties-common

# Thêm Docker GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Thêm Docker repository
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Cài đặt Docker
sudo apt update
sudo apt install docker-ce

# Thêm user vào docker group (để không cần sudo)
sudo usermod -aG docker $USER
```

### CentOS/RHEL:
```bash
# Cài đặt dependencies
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

# Thêm Docker repository
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Cài đặt Docker
sudo yum install docker-ce

# Khởi động Docker service
sudo systemctl start docker
sudo systemctl enable docker
```

### Windows/Mac:
Tải Docker Desktop từ trang chủ Docker và cài đặt theo hướng dẫn.

### Kiểm tra cài đặt:
```bash
docker --version
docker run hello-world
```

---

## Các khái niệm cơ bản

### 1. Image
- Template chỉ đọc để tạo containers
- Được xây dựng từ layers
- Có thể được chia sẻ qua Docker Registry

### 2. Container
- Instance chạy của một image
- Có thể được khởi động, dừng, xóa
- Cô lập với host system và containers khác

### 3. Dockerfile
- Text file chứa instructions để build image
- Mỗi instruction tạo một layer mới

### 4. Registry
- Kho lưu trữ images (Docker Hub, AWS ECR, etc.)
- Public hoặc private

### 5. Volume
- Cơ chế lưu trữ dữ liệu persistent
- Tồn tại độc lập với container lifecycle

---

## Làm việc với Images

### Tìm kiếm images:
```bash
# Tìm image trên Docker Hub
docker search nginx
docker search ubuntu
```

### Pull images:
```bash
# Pull image mới nhất
docker pull nginx

# Pull version cụ thể
docker pull nginx:1.20-alpine

# Pull từ registry khác
docker pull gcr.io/project-id/image-name
```

### Liệt kê images:
```bash
# Xem tất cả images
docker images

# Xem với format cụ thể
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

### Xóa images:
```bash
# Xóa image cụ thể
docker rmi nginx:latest

# Xóa image bằng ID
docker rmi 5a877c6b8a34

# Xóa tất cả unused images
docker image prune

# Xóa tất cả images
docker rmi $(docker images -q)
```

### Inspect image:
```bash
# Xem thông tin chi tiết
docker inspect nginx:latest

# Xem history của image
docker history nginx:latest
```

---

## Làm việc với Containers

### Tạo và chạy containers:
```bash
# Chạy container cơ bản
docker run nginx

# Chạy container trong background
docker run -d nginx

# Chạy với port mapping
docker run -d -p 8080:80 nginx

# Chạy với name cụ thể
docker run -d --name my-nginx -p 8080:80 nginx

# Chạy với environment variables
docker run -d -e MYSQL_ROOT_PASSWORD=secret mysql:5.7

# Chạy với volume mount
docker run -d -v /host/path:/container/path nginx

# Chạy interactive terminal
docker run -it ubuntu:20.04 /bin/bash
```

### Quản lý containers:
```bash
# Xem containers đang chạy
docker ps

# Xem tất cả containers (bao gồm stopped)
docker ps -a

# Dừng container
docker stop container_name_or_id

# Khởi động lại container
docker restart container_name_or_id

# Xóa container
docker rm container_name_or_id

# Xóa container đang chạy (force)
docker rm -f container_name_or_id

# Xóa tất cả stopped containers
docker container prune
```

### Tương tác với containers:
```bash
# Exec vào container đang chạy
docker exec -it container_name /bin/bash

# Copy file từ host vào container
docker cp /host/file.txt container_name:/path/in/container/

# Copy file từ container ra host
docker cp container_name:/path/in/container/file.txt /host/path/

# Xem logs
docker logs container_name

# Follow logs real-time
docker logs -f container_name

# Xem resource usage
docker stats container_name
```

### Container networking:
```bash
# Chạy container với custom network
docker network create my-network
docker run -d --network my-network --name app nginx

# Connect container đang chạy vào network
docker network connect my-network existing_container
```

---

## Dockerfile

Dockerfile là file text chứa các instructions để build Docker image.

### Cấu trúc cơ bản:
```dockerfile
# Base image
FROM ubuntu:20.04

# Metadata
LABEL maintainer="your-email@example.com"
LABEL version="1.0"

# Environment variables
ENV APP_HOME=/app
ENV NODE_ENV=production

# Working directory
WORKDIR $APP_HOME

# Copy files
COPY package*.json ./
COPY . .

# Run commands during build
RUN apt-get update && \
    apt-get install -y nodejs npm && \
    npm install && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Expose port
EXPOSE 3000

# Default user (security)
USER node

# Startup command
CMD ["npm", "start"]
```

### Các instructions quan trọng:

#### FROM
```dockerfile
# Base image
FROM node:16-alpine

# Multi-stage build
FROM node:16 AS builder
# Build stage commands...

FROM nginx:alpine AS production
# Production stage commands...
```

#### RUN vs CMD vs ENTRYPOINT
```dockerfile
# RUN: Thực thi trong build time
RUN apt-get update && apt-get install -y curl

# CMD: Default command, có thể override
CMD ["nginx", "-g", "daemon off;"]

# ENTRYPOINT: Always executed, không thể override
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
```

#### COPY vs ADD
```dockerfile
# COPY: Chỉ copy files/folders
COPY src/ /app/src/

# ADD: Copy + extract + download từ URL
ADD https://example.com/file.tar.gz /app/
ADD archive.tar.gz /app/
```

### Best practices cho Dockerfile:
```dockerfile
# 1. Sử dụng official base images
FROM node:16-alpine

# 2. Minimize layers bằng cách gộp RUN commands
RUN apt-get update && \
    apt-get install -y package1 package2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# 3. Leverage build cache
# Copy package.json trước để cache npm install
COPY package*.json ./
RUN npm install
COPY . .

# 4. Sử dụng .dockerignore
# Tạo file .dockerignore để exclude files không cần thiết

# 5. Multi-stage builds
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

### Build image:
```bash
# Build từ Dockerfile trong thư mục hiện tại
docker build -t my-app:latest .

# Build với custom Dockerfile name
docker build -f Dockerfile.prod -t my-app:prod .

# Build với build args
docker build --build-arg NODE_ENV=production -t my-app .

# Build no cache
docker build --no-cache -t my-app .
```

---

## Docker Compose

Docker Compose cho phép định nghĩa và chạy multi-container applications bằng YAML file.

### Cài đặt Docker Compose:
```bash
# Linux
sudo curl -L "https://github.com/docker/compose/releases/download/v2.12.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Kiểm tra
docker-compose --version
```

### docker-compose.yml cơ bản:
```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
    volumes:
      - .:/app
      - /app/node_modules
    depends_on:
      - db
    networks:
      - app-network

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network

  redis:
    image: redis:alpine
    networks:
      - app-network

volumes:
  postgres_data:

networks:
  app-network:
    driver: bridge
```

### Compose commands:
```bash
# Start services
docker-compose up

# Start in background
docker-compose up -d

# Build và start
docker-compose up --build

# Stop services
docker-compose down

# Stop và remove volumes
docker-compose down -v

# View logs
docker-compose logs

# Follow specific service logs
docker-compose logs -f web

# Scale services
docker-compose up --scale web=3

# Execute command trong service
docker-compose exec web bash

# View running services
docker-compose ps
```

### Environment variables:
```yaml
# .env file
DB_PASSWORD=secret
API_KEY=your-api-key

# docker-compose.yml
services:
  web:
    environment:
      - DB_PASSWORD=${DB_PASSWORD}
      - API_KEY=${API_KEY}
```

### Override files:
```yaml
# docker-compose.override.yml (tự động load)
version: '3.8'
services:
  web:
    volumes:
      - .:/app
    environment:
      - DEBUG=true

# docker-compose.prod.yml (manual)
version: '3.8'
services:
  web:
    restart: always
    environment:
      - NODE_ENV=production
```

```bash
# Sử dụng override file
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up
```

---

## Volumes và Networks

### Docker Volumes

Volumes cung cấp persistent storage cho containers.

#### Các loại volumes:
```bash
# 1. Named volumes
docker volume create my-volume
docker run -v my-volume:/data nginx

# 2. Bind mounts
docker run -v /host/path:/container/path nginx

# 3. Anonymous volumes
docker run -v /container/path nginx
```

#### Volume commands:
```bash
# Tạo volume
docker volume create my-vol

# Liệt kê volumes
docker volume ls

# Inspect volume
docker volume inspect my-vol

# Xóa volume
docker volume rm my-vol

# Xóa unused volumes
docker volume prune
```

#### Sử dụng trong docker-compose:
```yaml
services:
  db:
    image: postgres
    volumes:
      - db_data:/var/lib/postgresql/data  # Named volume
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql  # Bind mount

volumes:
  db_data:
```

### Docker Networks

Networks cho phép containers giao tiếp với nhau.

#### Network drivers:
- **bridge**: Default, containers trên cùng host
- **host**: Container sử dụng host network
- **overlay**: Multi-host networking (Swarm)
- **none**: Disable networking

#### Network commands:
```bash
# Tạo network
docker network create my-network
docker network create --driver bridge my-bridge

# Liệt kê networks
docker network ls

# Inspect network
docker network inspect my-network

# Connect container vào network
docker network connect my-network container_name

# Disconnect container khỏi network
docker network disconnect my-network container_name

# Xóa network
docker network rm my-network
```

#### Sử dụng trong docker-compose:
```yaml
services:
  web:
    networks:
      - frontend
      - backend
  
  db:
    networks:
      - backend

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true  # Chỉ internal communication
```

---

## Best Practices

### Security
```dockerfile
# 1. Sử dụng non-root user
FROM node:16-alpine
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001
USER nextjs

# 2. Scan vulnerabilities
# docker scan my-image:latest

# 3. Use multi-stage builds
FROM node:16 AS builder
# Build stage
FROM node:16-alpine AS runtime
COPY --from=builder /app/dist ./dist
```

### Performance
```dockerfile
# 1. Minimize layers
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# 2. Use .dockerignore
# .dockerignore
node_modules
.git
*.md
.env
```

### Resource Management
```bash
# Limit container resources
docker run -m 512m --cpus="1.5" nginx

# In docker-compose
services:
  web:
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: '1.5'
```

### Monitoring
```bash
# Container stats
docker stats

# System-wide info
docker system df
docker system info

# Clean up
docker system prune -a
```

---

## Troubleshooting

### Common Issues và Solutions

#### 1. Container exits immediately
```bash
# Check logs
docker logs container_name

# Check if command is running in foreground
# Bad: CMD service nginx start
# Good: CMD ["nginx", "-g", "daemon off;"]
```

#### 2. Permission denied
```bash
# Check file permissions
ls -la /path/to/file

# Fix trong Dockerfile
RUN chown -R user:group /app
USER user
```

#### 3. Port already in use
```bash
# Check what's using the port
sudo netstat -tulpn | grep :8080
sudo lsof -i :8080

# Use different port
docker run -p 8081:80 nginx
```

#### 4. Out of disk space
```bash
# Clean up
docker system prune -a
docker volume prune
docker image prune -a

# Check disk usage
docker system df
```

#### 5. Build context too large
```dockerfile
# Use .dockerignore
node_modules
.git
*.log
```

#### 6. Can't connect to Docker daemon
```bash
# Check if Docker is running
sudo systemctl status docker

# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

### Debug Techniques
```bash
# 1. Interactive debugging
docker run -it --rm image_name /bin/bash

# 2. Override entrypoint
docker run -it --entrypoint="" image_name /bin/bash

# 3. Check container processes
docker exec container_name ps aux

# 4. Network debugging
docker exec container_name netstat -tulpn
docker exec container_name ping other_container

# 5. File system debugging
docker exec container_name ls -la /path
docker exec container_name cat /path/to/config
```

### Health Checks
```dockerfile
# Trong Dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost/ || exit 1

# Trong docker-compose
services:
  web:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
```

---

### Lênh lưu ý
```bash
cat ./Dockerfile            # xem thư mục
mkdir test              # Tạo thư mục test
rmdir test                 # xóa thư mục
nano file                 # chỉnh sủa file
nano Dockerfile            # Chỉnh sửa
cd ../
cd ./<fileName>
docker ps              # Xem tiến trình (container)
docker images          # Xem mẫu (image)
docker build -t IMAGE-NAME
docker run -d --name -p 3000:3000 IAMGE-NAME # -d: chay nền
docker login
docker info
docker push IMAGE
docker pull YOUR NAME DOCKER /IMAGE
```
> [!NOTE]
> - `Dockerfile`: là bảng dựng sẵn để chạy `IMAGE`
> - `Image`: là mẫu để chạy `CONTAINER`
> - `Container`: là 1 tiến trình để chạy

## Tài liệu tham khảo

- [Docker Official Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Dockerfile Best Practices](https://docs.docker.com/develop/dev-best-practices/)

---

*Tài liệu này cung cấp kiến thức từ cơ bản đến nâng cao về Docker. Hãy thực hành thường xuyên để thành thạo các concepts và commands!*
