# Bài 04 - Docker Port Mapping

> **Status: Completed ✅**

## Port mapping là gì?

Container có network environment riêng. Một application listen trên port bên trong container không tự động có nghĩa là port đó chính là port trên host.

Ví dụ nginx listen trên port `80` trong container:

```bash
docker run --name my-nginx -d -p 8080:80 nginx
```

Cú pháp:

```text
-p HOST_PORT:CONTAINER_PORT
```

Ví dụ:

```text
-p 8080:80
   │    │
   │    └── Container port
   └─────── Host port
```

Flow:

```text
Browser
   │
   ▼
localhost:8080
   │
   ▼
Host :8080
   │
   │ Docker forwards
   ▼
Container :80
   │
   ▼
nginx
```

## Host port không cần giống container port

Các mapping sau đều hợp lệ:

```bash
-p 8080:80
-p 3000:80
-p 5000:80
```

Nginx vẫn listen trên `80` trong container. Chỉ có port dùng để truy cập từ host thay đổi.

## Nhiều container có thể dùng cùng internal port

```bash
docker run --name nginx-1 -d -p 8080:80 nginx
docker run --name nginx-2 -d -p 8081:80 nginx
```

```text
Host :8080 ──────► nginx-1 :80
Host :8081 ──────► nginx-2 :80
```

Hai container đều có thể dùng port `80` vì mỗi container có network environment riêng.

Nhưng hai container không thể cùng bind một host port theo cùng cách:

```bash
docker run --name nginx-3 -d -p 8080:80 nginx
```

Nếu `nginx-1` đã dùng host port `8080`, container thứ ba sẽ không start thành công vì port conflict.

## `Created` vs `Running` vs `Exited`

Experiment port conflict cho thấy `docker run` có thể hiểu đơn giản là:

```text
docker run ≈ docker create + docker start
```

Nếu container được create thành công nhưng start thất bại do port conflict, nó có thể còn ở trạng thái:

```text
Created
```

Lifecycle đơn giản:

```text
Created
   │ docker start
   ▼
Running
   │ process kết thúc / docker stop
   ▼
Exited
```

## Image tag mặc định

Khi chạy:

```bash
docker run nginx
```

Docker mặc định hiểu image reference là:

```text
nginx:latest
```

Tương tự:

```text
postgres → postgres:latest
```

Có thể chọn tag/version cụ thể:

```bash
docker run postgres:17
```

`latest` chỉ là một tag có tên `latest`, không nên hiểu rằng Docker tự động chọn version mới nhất theo một cơ chế đặc biệt.

## `EXPOSE` khác `-p`

Dockerfile có thể có:

```dockerfile
EXPOSE 80
```

Nó mô tả port mà application dự kiến sử dụng. Nó không tương đương với việc publish port ra host.

Port publishing được thực hiện bằng:

```bash
docker run -p 8080:80 nginx
```

## `localhost` và container

Nếu FE và BE nằm trong cùng một container/network namespace thì các process có thể giao tiếp qua `localhost` và các port khác nhau.

Nhưng thông thường Docker tách các service thành container riêng:

```text
FE container
BE container
PostgreSQL container
Redis container
```

Khi đó `localhost` bên trong FE container chỉ trỏ tới chính FE container, không phải BE container. Container-to-container communication sẽ được học kỹ ở bài Docker Networking.

## PostgreSQL example

Nếu PostgreSQL listen ở container port `5432`, nhưng muốn truy cập từ Mac qua `localhost:9999`:

```bash
docker run --name my-postgres -d -p 9999:5432 -e POSTGRES_PASSWORD=my-password postgres
```

Mapping:

```text
localhost:9999 ──────► PostgreSQL container :5432
```

## Commands đã thực hành

```bash
docker run --name my-nginx -d -p 8080:80 nginx
docker ps
docker ps -a

docker run --name nginx-1 -d -p 8080:80 nginx
docker run --name nginx-2 -d -p 8081:80 nginx

docker rm -f nginx-1 nginx-2 nginx-3
```

## Key takeaways

```text
-p HOST_PORT:CONTAINER_PORT
```

- Container có network environment riêng.
- Nhiều container có thể dùng cùng container port.
- Host ports dùng đồng thời phải không conflict.
- `docker run` có thể hình dung là `create + start`.
- Không ghi tag thì Docker mặc định dùng `:latest`.
- `EXPOSE` không tự publish port ra host.

## Trạng thái

**Completed ✅**

← [Bài 03](../03-container-lifecycle/README.md) | [Bài 05 - Volumes](../05-volumes/README.md) →
