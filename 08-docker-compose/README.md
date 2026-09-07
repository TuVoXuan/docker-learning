# Bài 08 - Docker Compose

> **Status: Not started ⬜**

## Nội dung sẽ học

- Docker Compose giải quyết vấn đề gì?
- `compose.yaml`.
- `services`.
- `image` và `build`.
- `ports`.
- `volumes`.
- `environment`.
- `depends_on`.
- Network mặc định của Compose.

## Mục tiêu

Chạy một stack gần với project thực tế:

```text
Frontend / API
      │
      ├── PostgreSQL
      ├── Redis
      └── MinIO
```

Commands chính:

```bash
docker compose up
docker compose up -d
docker compose ps
docker compose logs
docker compose down
```

← [Bài 07](../07-dockerfile/README.md) | [Bài 09](../09-environment-variables/README.md) →
