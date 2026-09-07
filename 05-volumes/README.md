# Bài 05 - Docker Volumes

> **Status: Not started ⬜**

## Nội dung sẽ học

- Container filesystem.
- Tại sao dữ liệu có thể mất khi remove container.
- Volume là gì?
- Named volume.
- Bind mount.
- Volume với PostgreSQL.
- Khi nào dùng volume và khi nào dùng bind mount.

## Ví dụ sẽ thực hành

```bash
docker volume ls
docker volume create my-data
```

Sau đó chúng ta sẽ chạy PostgreSQL với persistent data.

← [Bài 04](../04-port-mapping/README.md) | [Bài 06](../06-networking/README.md) →
