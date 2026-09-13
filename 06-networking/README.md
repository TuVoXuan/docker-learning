# Bài 06 - Docker Networking

> **Status: Completed ✅**

## Docker Network là gì?

Docker Network cho phép containers giao tiếp với nhau và đồng thời tạo network isolation giữa các nhóm container.

Mỗi container có network environment riêng, vì vậy `localhost` bên trong một container luôn trỏ về chính container đó.

```text
backend container: localhost → backend itself
postgres container: localhost → postgres itself
```

Do đó backend không dùng `localhost:5432` để truy cập PostgreSQL ở container khác.

## Network mặc định

```bash
docker network ls
```

Docker có các network mặc định như:

- `bridge` — network mặc định cho container thông thường.
- `host` — sử dụng host networking; behavior trên Docker Desktop macOS khác native Linux.
- `none` — container không có external networking thông thường.

Trong bài này tập trung vào bridge networking và user-defined bridge network.

## User-defined bridge network

Tạo network:

```bash
docker network create my-network
```

Chạy container trong network:

```bash
docker run --name backend --network my-network -d nginx
```

Một container khác cùng network:

```bash
docker run --name client --network my-network -it alpine sh
```

## Docker DNS và container name

Trong `client`:

```bash
ping backend
```

Experiment thực tế resolve:

```text
backend → 172.18.0.2
```

Docker DNS cho phép dùng container/service name thay vì hard-code container IP.

```text
backend
   ↓ Docker DNS
current IP of backend container
```

Không nên hard-code IP như `172.18.0.2`, vì container có thể được recreate và nhận IP khác.

## HTTP giữa containers không cần publish port

Từ `client`:

```bash
wget -qO- http://backend
```

Nginx trả về `Welcome to nginx!` dù `backend` không được chạy với `-p`.

Điều này cho thấy containers cùng Docker network có thể giao tiếp trực tiếp qua container port:

```text
client → backend:80
```

`-p` cần khi muốn publish container port ra host:

```text
-p 8080:80

Mac localhost:8080 → container :80
```

Mental model:

```text
-p      → Host ↔ Container
network → Container ↔ Container
```

## Network isolation

Một `outsider` container được tạo mà không có `--network my-network`:

```bash
docker run --name outsider -it alpine sh
```

Nó nằm trên default bridge và không resolve được `backend`:

```text
ping: bad address 'backend'
wget: bad address 'backend'
```

Sau khi connect nó vào `my-network`:

```bash
docker network connect my-network outsider
```

`outsider` có thể resolve và ping `backend`.

Một container có thể tham gia nhiều network cùng lúc.

## Inspect network

```bash
docker network inspect my-network
```

Phần `Containers` cho biết những container đang attached vào network và network address của chúng.

## PostgreSQL experiment

Chạy PostgreSQL trong `my-network` mà không publish port:

```bash
docker volume create network-postgres-data

docker run \
  --name network-postgres \
  --network my-network \
  -d \
  -e POSTGRES_PASSWORD=123456 \
  -v network-postgres-data:/var/lib/postgresql \
  postgres
```

Chạy PostgreSQL client trong cùng network:

```bash
docker run \
  --name postgres-client \
  --network my-network \
  -it \
  postgres \
  psql -h network-postgres -U postgres
```

Client kết nối thành công bằng:

```text
network-postgres:5432
```

mà PostgreSQL không cần `-p 5432:5432`.

Nếu PostgreSQL chỉ được backend container sử dụng thì không nhất thiết phải publish database port ra host.

Nếu một application trên Mac như DBeaver cần truy cập PostgreSQL, lúc đó có thể publish port và dùng `localhost:<HOST_PORT>`.

## Disconnect network

```bash
docker network disconnect my-network postgres-client
```

Khi containers không còn shared network, hostname của container kia không thể được resolve thông qua network đó.

## Debugging mental model

Khi gặp lỗi như:

```text
bad address 'backend'
ENOTFOUND postgres
could not translate host name
getaddrinfo ...
```

một trong những câu hỏi đầu tiên nên kiểm tra là:

```text
Hai container có cùng Docker network không?
```

Kiểm tra bằng:

```bash
docker network inspect my-network
docker inspect CONTAINER_NAME
```

## Project thực tế

Ví dụ backend cùng network với PostgreSQL, Redis và MinIO:

```env
DB_HOST=postgres
DB_PORT=5432
REDIS_HOST=redis
REDIS_PORT=6379
MINIO_HOST=minio
MINIO_PORT=9000
```

Không dùng `localhost` cho các service nằm trong container khác.

## Commands đã thực hành

```bash
docker network ls
docker network create my-network
docker network inspect my-network
docker network connect my-network CONTAINER
docker network disconnect my-network CONTAINER

docker run --network my-network IMAGE
```

## Key takeaways

- `localhost` trong container trỏ về chính container đó.
- Containers cùng user-defined network có thể giao tiếp bằng container/service name.
- Docker DNS resolve name sang IP hiện tại của container.
- Không nên hard-code container IP.
- Container-to-container communication không cần publish port ra host.
- `-p` dùng để expose/publish container port cho host truy cập.
- Network tạo cả communication lẫn isolation.
- Một container có thể connected vào nhiều network.
- Backend thường kết nối database bằng hostname như `postgres:5432`, không phải `localhost:5432`.

## Trạng thái

**Completed ✅**

← [Bài 05 - Volumes](../05-volumes/README.md) | [Bài 07 - Dockerfile](../07-dockerfile/README.md) →
