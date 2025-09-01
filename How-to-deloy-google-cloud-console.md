# Hướng dẫn Deploy ứng dụng Welcome-to-Docker trên Google Cloud Console

## Các bước thực hiện

### 1. Clone dự án
```bash
git clone https://github.com/docker/welcome-to-docker.git
cd ./welcome-to-docker
```

### 2. Build Docker image
```bash
# Build image với tên welcome-to-docker
docker build -t welcome-to-docker .

# Kiểm tra image đã được tạo
docker images
```

### 3. Chạy container
```bash
# Chạy container với port 8080
docker run -d -p 8080:3000 --name myapp-container welcome-to-docker

# Kiểm tra container đang chạy
docker ps
```

### 4. Mở port và truy cập ứng dụng

1. Trên Google Cloud Console, tìm biểu tượng mở port ở góc trên bên phải
2. Mở port 8080
3. Truy cập ứng dụng qua URL được cung cấp

> [!TIP]
>
> Nếu bạn thấy trang web hiển thị, chúc mừng bạn đã deploy thành công!

