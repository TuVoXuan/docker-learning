# Bài 03 - Container Lifecycle

## Mục tiêu

Hiểu lifecycle của container và phân biệt `docker run` với `docker start`.

## Detached mode `-d`

```bash
docker run --name my-nginx -d nginx
```

Trong đó:

```text
docker run
   │
   ├── --name my-nginx → đặt tên container
   ├── -d              → detached mode
   └── nginx           → image
```

`-d` cho container chạy ở background và trả terminal lại cho bạn.

## Lifecycle

```text
                  docker run
                      │
                      ▼
                   Running
                   ↙     ↖
        docker stop     docker start
                 ↘       ↙
                   Stopped
                      │
                  docker rm
                      │
                      ▼
                   Removed
```

## Stop

```bash
docker stop my-nginx
```

Stop **không xóa container**.

Sau khi stop:

```bash
docker ps
```

không thấy container, nhưng:

```bash
docker ps -a
```

vẫn thấy container ở trạng thái `Exited`.

## Start

```bash
docker start my-nginx
```

Start chạy lại **container đã tồn tại**.

Điểm quan trọng:

```text
docker run   → create NEW container + start
docker start → start EXISTING container
```

## Restart

```bash
docker restart my-nginx
```

Có thể hiểu đơn giản là stop rồi start lại container.

## Remove

Container đã stop:

```bash
docker rm my-nginx
```

Force remove container đang chạy:

```bash
docker rm -f my-nginx
```

Xóa container không đồng nghĩa với xóa image.

```text
nginx IMAGE
    │
    └── my-nginx CONTAINER
             │
          docker rm
             ▼
        container mất

nginx IMAGE vẫn còn
```

## `--rm`

```bash
docker run --rm hello-world
```

Container sẽ tự bị xóa sau khi process kết thúc.

Rất hữu ích cho các container chỉ cần chạy một lần.

## Xóa image

```bash
docker rmi nginx
```

`rmi` có thể nhớ là **remove image**.

## Practice

```bash
docker run --name my-nginx -d nginx

docker ps

docker stop my-nginx

docker ps

docker ps -a

docker start my-nginx

docker restart my-nginx

docker rm -f my-nginx

docker ps -a

docker images
```

## Review

Hãy tự trả lời trước khi xem lại nội dung phía trên:

1. `-d` có tác dụng gì?
2. `docker stop` có xóa container không?
3. `docker run` và `docker start` khác nhau ở điểm quan trọng nào?
4. `docker rm` có xóa image không?
5. Khi nào `--rm` hữu ích?
6. Main process kết thúc thì chuyện gì xảy ra với container?

## Key takeaway

> `docker run` tạo container mới. `docker start` chạy lại container đã tồn tại.

và:

> Main process còn chạy → container còn running. Main process kết thúc → container dừng.

## Trạng thái

**Completed ✅**

← [Bài 02](../02-images-and-containers/README.md) | [Bài 04 - Port Mapping](../04-port-mapping/README.md) →
