# Bài 02 - Image và Container

## Mục tiêu

Hiểu rõ sự khác nhau giữa Docker Image và Docker Container.

## Image là gì?

Image có thể hiểu là một **template chỉ đọc** chứa những thứ cần thiết để tạo container.

Ví dụ:

```text
hello-world
nginx
postgres:17
redis
node:22
```

Xem image đang có trên máy:

```bash
docker images
```

## Container là gì?

Container là một instance được tạo từ image.

Nếu quen OOP, có thể dùng analogy sau để dễ nhớ:

```text
Image     ≈ Class
Container ≈ Instance
```

Đây chỉ là cách hình dung, không phải cách Docker được implement.

Một image có thể tạo nhiều container:

```text
             hello-world
                IMAGE
                  │
          ┌───────┴───────┐
          ▼               ▼
     Container A     Container B
```

Mỗi lần chạy:

```bash
docker run hello-world
```

Docker tạo **một container mới**.

## `docker ps`

```bash
docker ps
```

Chỉ hiển thị container đang chạy.

```bash
docker ps -a
```

Hiển thị tất cả container, kể cả container đã dừng.

## Tại sao hello-world là Exited?

Container tồn tại ở trạng thái running khi **main process của container còn chạy**.

`hello-world` chạy command:

```text
/hello
```

Flow:

```text
Container starts
      │
      ▼
Run /hello
      │
      ▼
Print "Hello from Docker!"
      │
      ▼
/hello finishes
      │
      ▼
Container stops
```

Vì vậy `docker ps -a` hiển thị:

```text
Exited (0)
```

`0` là exit code thành công theo convention Unix/Linux.

`Exited` không nhất thiết có nghĩa là container bị crash.

## Container name

Nếu không đặt tên, Docker tự generate tên như:

```text
intelligent_greider
jolly_hypatia
```

Có thể tự đặt tên:

```bash
docker run --name my-hello hello-world
```

## Commands cần nhớ

```bash
docker images
docker ps
docker ps -a
docker run hello-world
docker run --name my-hello hello-world
```

## Review

1. Image và Container khác nhau thế nào?
2. Một image có thể tạo nhiều container không?
3. Mỗi lần `docker run` có tạo container mới không?
4. Tại sao `hello-world` chuyển sang `Exited`?
5. `Exited (0)` có nghĩa là bị lỗi không?
6. `docker ps` khác `docker ps -a` ở đâu?

## Trạng thái

**Completed ✅**

← [Bài 01](../01-getting-started/README.md) | [Bài 03 - Container Lifecycle](../03-container-lifecycle/README.md) →
