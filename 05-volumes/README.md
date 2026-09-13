# Bài 05 - Docker Volumes

> **Status: Completed ✅**

## Volume giải quyết vấn đề gì?

Container có writable filesystem riêng. Nếu dữ liệu quan trọng chỉ nằm trong filesystem của container thì không nên phụ thuộc vào lifecycle của container để giữ dữ liệu.

Docker Volume cho phép persistent data có lifecycle độc lập với container:

```text
Container lifecycle ≠ Data lifecycle
```

Xóa container không tự động xóa named volume đã được tạo riêng.

## Named Volume

Tạo và kiểm tra volume:

```bash
docker volume create postgres-data
docker volume ls
docker volume inspect postgres-data
```

Mount volume vào container:

```text
-v VOLUME_NAME:CONTAINER_PATH
```

Với PostgreSQL 18+:

```bash
docker run \
  --name my-postgres \
  -d \
  -e POSTGRES_PASSWORD=123456 \
  -v postgres-data:/var/lib/postgresql \
  postgres
```

> PostgreSQL 18+ official image dùng layout data mới và nên mount tại `/var/lib/postgresql`. Các tutorial/version cũ thường dùng `/var/lib/postgresql/data`.

Docker quản lý location thực tế của named volume. Trên Docker Desktop for macOS, mountpoint như `/var/lib/docker/volumes/...` nằm trong Linux environment/VM do Docker Desktop quản lý, không phải trực tiếp trong filesystem macOS.

## Experiment: data sống lâu hơn container

1. Tạo `docker_learning` database trong `my-postgres`.
2. Xóa `my-postgres` nhưng giữ `postgres-data`.
3. Tạo container PostgreSQL mới và mount lại `postgres-data`.
4. Database `docker_learning` vẫn tồn tại.

Kết luận:

```text
Container #1 ──┐
               ├──► postgres-data
Container #2 ──┘
```

Nếu chạy:

```bash
docker volume rm postgres-data
```

thì volume cũ và data trong nó bị xóa. Tạo volume mới cùng tên không khôi phục data cũ.

## Bind Mount

Bind mount map một path cụ thể trên host vào container:

```bash
-v ./src:/app/src
```

Khác với named volume, người dùng quản lý host path trực tiếp. Thay đổi file trên host được container nhìn thấy ngay, và mặc định thay đổi từ container cũng có thể ghi ngược về host.

Experiment với nginx:

```bash
docker run \
  --name bind-nginx \
  -d \
  -p 8080:80 \
  -v "$(pwd)":/usr/share/nginx/html \
  nginx
```

Sửa `index.html` trên Mac và refresh browser cho thấy nginx đọc ngay nội dung mới mà không cần rebuild/restart container.

## Read-only bind mount

Thêm `:ro` để container chỉ được đọc mount:

```bash
-v "$(pwd)":/usr/share/nginx/html:ro
```

Container có thể đọc file nhưng không thể ghi vào mount. Host vẫn có thể sửa file bình thường.

## Named Volume vs Bind Mount

| | Named Volume | Bind Mount |
|---|---|---|
| Ví dụ | `postgres-data:/var/lib/postgresql` | `./src:/app/src` |
| Location | Docker quản lý | Người dùng quản lý |
| Host edit trực tiếp | Không phải mục đích chính | Có |
| Use case phổ biến | Database/persistent app data | Source code/config trong development |

Mental model:

```text
Named Volume: "Docker, giữ data này giúp tôi."
Bind Mount:   "Docker, dùng chính folder này trên máy tôi."
```

## Commands đã thực hành

```bash
docker volume create postgres-data
docker volume ls
docker volume inspect postgres-data
docker volume rm postgres-data

docker exec -it my-postgres psql -U postgres

docker run -v postgres-data:/var/lib/postgresql postgres
docker run -v "$(pwd)":/usr/share/nginx/html nginx
```

## Key takeaways

- Xóa container không đồng nghĩa xóa named volume.
- Xóa volume sẽ xóa persistent data của volume đó.
- Tạo volume mới cùng tên vẫn là volume mới.
- Named Volume do Docker quản lý và phù hợp với persistent application data.
- Bind Mount dùng path thật trên host và rất hữu ích cho source code/config trong development.
- Bind Mount mặc định read-write; `:ro` làm mount read-only từ phía container.
- Trên Docker Desktop macOS, Docker-managed volumes nằm trong Linux environment của Docker Desktop.

## Review

1. Xóa PostgreSQL container nhưng giữ volume → data vẫn còn.
2. Xóa volume rồi tạo volume mới cùng tên → data cũ không còn.
3. Named Volume do Docker quản lý; Bind Mount map host path do người dùng quản lý.
4. `./config:/app/config:ro` → container chỉ có quyền đọc mount.

## Trạng thái

**Completed ✅**

← [Bài 04](../04-port-mapping/README.md) | [Bài 06 - Docker Networking](../06-networking/README.md) →
