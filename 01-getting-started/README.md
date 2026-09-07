# Bài 01 - Cài đặt Docker trên macOS

## Mục tiêu

- Cài Docker Desktop.
- Kiểm tra Docker CLI.
- Kiểm tra Docker Compose.
- Chạy container đầu tiên.

## Kiểm tra kiến trúc Mac

```bash
uname -m
```

- `arm64`: Apple Silicon.
- `x86_64`: Intel.

Docker Desktop trên Mac cung cấp Docker CLI, Docker Engine environment và Docker Compose.

## Kiểm tra cài đặt

```bash
docker --version
docker compose version
```

Docker Compose hiện đại sử dụng:

```bash
docker compose
```

thay vì command cũ:

```bash
docker-compose
```

## Container đầu tiên

```bash
docker run hello-world
```

Kết quả thành công có dòng:

```text
Hello from Docker!
```

Docker đã thực hiện gần đúng flow sau:

```text
docker run hello-world
        │
        ▼
Tìm image hello-world ở local
        │
        ├── không có → pull image
        │
        ▼
Tạo container
        │
        ▼
Start container
        │
        ▼
Run /hello
```

## Review

1. Docker Desktop trên Mac cung cấp những thành phần nào chúng ta cần?
2. `docker compose` và `docker-compose` khác nhau như thế nào?
3. `docker run hello-world` có tạo container không?

## Trạng thái

**Completed ✅**

Tiếp theo: [Bài 02 - Image và Container](../02-images-and-containers/README.md)
