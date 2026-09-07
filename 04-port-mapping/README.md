# Bài 04 - Docker Port Mapping

> **Status: NEXT ⏭️**

Đây là bài tiếp theo trong lộ trình.

## Chúng ta sẽ học gì?

Command chính:

```bash
docker run --name my-nginx -d -p 8080:80 nginx
```

Sau bài này cần hiểu được:

```text
-p 8080:80
   │    │
   │    └── Container port
   └─────── Host port
```

Các concept sẽ học:

- Host là gì?
- Container port là gì?
- Host port là gì?
- Port mapping hoạt động thế nào?
- Tại sao nginx chạy trong container nhưng browser cần port mapping để truy cập?
- `localhost:8080` đang trỏ đến đâu?
- `0.0.0.0` có ý nghĩa gì?
- `EXPOSE` và publish port có giống nhau không?

## Practice dự kiến

```bash
docker run --name my-nginx -d -p 8080:80 nginx

docker ps
```

Sau đó truy cập:

```text
http://localhost:8080
```

## Trạng thái

**Not started ⬜**

← [Bài 03](../03-container-lifecycle/README.md) | [Bài 05 - Volumes](../05-volumes/README.md) →
