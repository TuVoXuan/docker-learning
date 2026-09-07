# Bài 07 - Dockerfile

> **Status: Not started ⬜**

## Nội dung sẽ học

- Dockerfile dùng để làm gì?
- `FROM`
- `WORKDIR`
- `COPY`
- `RUN`
- `CMD`
- `ENTRYPOINT`
- `EXPOSE`
- `.dockerignore`
- `docker build`
- Image layers và build cache.

## Mục tiêu thực hành

Tự build một Node.js application thành Docker image:

```bash
docker build -t my-node-app .
docker run my-node-app
```

← [Bài 06](../06-networking/README.md) | [Bài 08](../08-docker-compose/README.md) →
