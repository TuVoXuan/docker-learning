# Docker Learning

Repo này dùng để học Docker theo từng bài nhỏ, dễ review và dễ quay lại tiếp tục vào ngày hôm sau.

## Mục tiêu

Sau khi hoàn thành lộ trình này, bạn sẽ có thể:

- Hiểu rõ Image, Container và lifecycle của container.
- Biết cách chạy và quản lý service bằng Docker.
- Hiểu port mapping, volume và network.
- Tự viết Dockerfile cho ứng dụng Node.js/React/NestJS.
- Dùng Docker Compose để chạy nhiều service như API, PostgreSQL, Redis và MinIO.
- Biết cách debug các lỗi Docker thường gặp.
- Biết các best practice cơ bản khi đưa Docker lên production.

## Tiến độ

- [x] [Bài 01 - Cài đặt Docker trên macOS](./01-getting-started/README.md)
- [x] [Bài 02 - Image và Container](./02-images-and-containers/README.md)
- [x] [Bài 03 - Container Lifecycle](./03-container-lifecycle/README.md)
- [x] [Bài 04 - Port Mapping](./04-port-mapping/README.md)
- [x] [Bài 05 - Volumes](./05-volumes/README.md)
- [ ] [Bài 06 - Docker Networking](./06-networking/README.md) ← **Đang học**
- [ ] [Bài 07 - Dockerfile](./07-dockerfile/README.md)
- [ ] [Bài 08 - Docker Compose](./08-docker-compose/README.md)
- [ ] [Bài 09 - Environment Variables](./09-environment-variables/README.md)
- [ ] [Bài 10 - Debugging Docker](./10-debugging/README.md)
- [ ] [Bài 11 - Docker trong Production](./11-production/README.md)

## Cách học

Mỗi bài gồm 4 phần chính:

1. **Concept** — hiểu Docker đang làm gì.
2. **Commands** — các command quan trọng.
3. **Practice** — tự chạy thử trên máy.
4. **Review** — câu hỏi ngắn để kiểm tra lại kiến thức.

Không cần học thuộc command. Mục tiêu quan trọng hơn là hiểu vì sao command đó hoạt động.

## Cheat sheet hiện tại

```bash
# Kiểm tra Docker
docker --version
docker compose version

# Chạy container
docker run IMAGE

# Chạy background
docker run -d IMAGE

# Đặt tên container
docker run --name NAME IMAGE

# Publish port
docker run -p HOST_PORT:CONTAINER_PORT IMAGE

# Xem container đang chạy
docker ps

# Xem tất cả container
docker ps -a

# Stop / start / restart
docker stop NAME
docker start NAME
docker restart NAME

# Xóa container
docker rm NAME
docker rm -f NAME

# Xem image
docker images

# Xóa image
docker rmi IMAGE

# Volume
docker volume create NAME
docker volume ls
docker volume inspect NAME
docker volume rm NAME

# Named Volume
docker run -v VOLUME_NAME:CONTAINER_PATH IMAGE

# Bind Mount
docker run -v HOST_PATH:CONTAINER_PATH IMAGE
```

## Trạng thái hiện tại

Đã hoàn thành **Bài 05 - Docker Volumes**: persistent data, named volumes, bind mounts, read-only mounts và PostgreSQL persistence.

**Đang học: Bài 06 - Docker Networking.**
