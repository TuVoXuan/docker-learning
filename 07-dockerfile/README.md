# Bài 07 - Dockerfile

> **Status: Completed ✅**

## Dockerfile là gì?

Dockerfile là tập hợp instructions mô tả cách Docker build application thành một image.

```text
Source Code + Dockerfile
          │
          │ docker build
          ▼
        Image
          │
          │ docker run
          ▼
       Container
```

Mental model quan trọng:

```text
Dockerfile → docker build → Image → docker run → Container
```

## Dockerfile cơ bản

Ví dụ Node.js:

```dockerfile
FROM node:22

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
```

### `FROM`

```dockerfile
FROM node:22
```

Chọn base image. Application có thể sử dụng Node.js bên trong image mà không phụ thuộc Node.js được cài trên host.

### `WORKDIR`

```dockerfile
WORKDIR /app
```

Đặt working directory cho các instruction tiếp theo.

### `COPY`

```dockerfile
COPY index.js ./
COPY . .
```

Copy file từ build context vào filesystem của image tại build time.

`COPY` không tạo sync với source trên host. Nếu source thay đổi, image cũ không tự thay đổi và cần build lại.

### `RUN`

```dockerfile
RUN npm ci
RUN npm run build
```

Chạy command trong quá trình **build image**.

### `CMD`

```dockerfile
CMD ["node", "index.js"]
```

Default command chạy khi container start.

```text
RUN → build time
CMD → run time
```

`CMD` có thể bị override:

```bash
docker run --rm my-node-server node --version
```

### `ENTRYPOINT`

`ENTRYPOINT` thường xác định executable chính của image.

Ví dụ:

```dockerfile
ENTRYPOINT ["node"]
CMD ["--version"]
```

Khi chạy image không truyền thêm arguments, Docker thực hiện tương đương:

```bash
node --version
```

Có thể hiểu:

```text
ENTRYPOINT → executable chính
CMD        → default command/arguments
```

Với application container thông thường, chỉ dùng `CMD` để start application thường đã đủ.

### Exec form

Ưu tiên:

```dockerfile
CMD ["node", "index.js"]
```

thay vì shell form:

```dockerfile
CMD node index.js
```

Exec form giúp process và signal handling rõ ràng hơn.

### `EXPOSE`

```dockerfile
EXPOSE 3000
```

Mô tả/document port application dự kiến listen bên trong container. Nó không publish port ra host.

Muốn Mac truy cập container vẫn cần:

```bash
docker run -p 8080:3000 IMAGE
```

## Build image

```bash
docker build -t my-node-app .
```

Trong đó:

```text
docker build       build image
-t my-node-app     đặt tag/name
.                  build context
```

Nếu không chỉ định tag version thì `my-node-app` tương đương `my-node-app:latest`.

## Build Context

Dấu `.` trong:

```bash
docker build .
```

là build context, không phải Dockerfile.

Dockerfile và build context là hai concept khác nhau:

```bash
docker build -f Dockerfile.production .
```

```text
-f Dockerfile.production → Dockerfile được sử dụng
.                        → build context
```

## `.dockerignore`

Ví dụ:

```text
node_modules
.git
.env
dist
npm-debug.log
```

`.dockerignore` loại các file không cần thiết khỏi Docker build context.

Nó giúp tránh đưa những thứ như local `node_modules`, Git metadata hoặc `.env` vào build context/image ngoài ý muốn.

## Image layers và Build Cache

Docker có thể reuse cache của các build steps khi input của chúng không thay đổi.

Node.js Dockerfile nên thường tách dependency files khỏi source:

```dockerfile
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
```

Nếu chỉ sửa `index.js` hoặc source code:

```text
COPY package files   CACHED ✅
RUN npm ci           CACHED ✅
COPY source          REBUILD 🔄
```

Nếu viết:

```dockerfile
COPY . .
RUN npm install
```

thì thay đổi source có thể invalidate cache của dependency installation phía sau.

Thứ tự instruction vì vậy ảnh hưởng đáng kể đến build performance.

## `npm ci` trong Docker

Khi project có lock file, `npm ci` phù hợp với clean/automated builds vì dependencies được cài dựa chặt vào lock file.

```dockerfile
COPY package.json package-lock.json ./
RUN npm ci
```

## Multi-stage Build

React/Vite application cần Node.js để build nhưng production runtime chỉ cần static output.

Dockerfile thực hành:

```dockerfile
FROM node:22 AS builder

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80
```

Flow:

```text
Stage 1 — Builder

node:22
  │
  ├── npm ci
  ├── source code
  └── npm run build
          │
          ▼
        dist/
          │
          │ COPY --from=builder
          ▼
Stage 2 — Runtime

nginx:alpine
     +
   dist/
     │
     ▼
Final Image
```

Final React image không cần Node.js, npm, TypeScript, Vite hay source code. Nó chỉ cần Nginx và static build output.

Experiment trong final container:

```bash
docker exec -it my-react-app sh
node --version
npm --version
ls -la /usr/share/nginx/html
```

Node/npm không tồn tại trong final Nginx image, nhưng `index.html` và `assets/` tồn tại.

Multi-stage build giúp tách build environment khỏi runtime environment, thường tạo final image nhỏ và ít tooling/dependency không cần thiết hơn.

## Image tags

Có thể build nhiều version:

```bash
docker build -t my-react-app:1.0 .
docker build -t my-react-app:2.0 .
```

`latest` chỉ là một tag; nó không phải cơ chế tự động chọn version mới nhất.

## Build image mới không update container cũ

Một container được tạo từ một specific image tại thời điểm `docker run`.

```text
Image v1
   │
   └── Container A

Build Image v2
   │
   └── Container A vẫn không tự đổi
```

`docker restart` chỉ stop/start cùng container và không recreate container từ image mới.

Để chạy image mới:

```bash
docker rm -f my-react-app

docker run \
  --name my-react-app \
  -d \
  -p 8080:80 \
  my-react-app:2.0
```

Deployment mental model:

```text
Code changed
     ↓
Build new image
     ↓
Remove/recreate old container
     ↓
New container runs new image
```

Version tags cũng giúp lựa chọn version cụ thể và hỗ trợ rollback về image cũ khi cần.

## Experiments đã hoàn thành

- Build Node.js image đầu tiên.
- Sửa source và chứng minh image không tự cập nhật.
- Rebuild image để nhận source mới.
- Quan sát Docker build cache.
- Build Express server và publish `8080:3000`.
- Override `CMD` bằng command sau image name.
- Dùng `.dockerignore`.
- Multi-stage build React/Vite với Node builder + Nginx runtime.
- Inspect final container và xác nhận Node/npm không tồn tại.
- Build React image version mới và chứng minh container cũ không tự cập nhật.
- Restart container cũ và xác nhận vẫn dùng filesystem/image cũ.
- Recreate container từ image `2.0` để chạy version mới.

## Commands đã thực hành

```bash
# Build image
docker build -t IMAGE_NAME .
docker build -t IMAGE_NAME:TAG .

# Chọn Dockerfile khác
docker build -f Dockerfile.production -t IMAGE_NAME .

# Run image
docker run IMAGE_NAME
docker run --rm IMAGE_NAME

# Override CMD
docker run --rm IMAGE_NAME COMMAND ARGUMENTS

# Inspect
docker image inspect IMAGE_NAME
docker inspect CONTAINER_NAME

# Vào container
docker exec -it CONTAINER_NAME sh
```

## Key takeaways

- Dockerfile mô tả cách build image.
- `FROM` chọn base image.
- `RUN` chạy lúc build; `CMD` chạy lúc container start.
- `COPY` copy source vào image, không sync source với container.
- Build context quyết định Docker có thể sử dụng những file nào khi build.
- `.dockerignore` loại file không cần khỏi build context.
- Thứ tự Dockerfile instruction ảnh hưởng build cache.
- Multi-stage build tách builder khỏi final runtime image.
- Image mới không tự cập nhật container đã tồn tại.
- `docker restart` không recreate container.
- Image tags cho phép quản lý nhiều version của application.

## Trạng thái

**Completed ✅**

← [Bài 06 - Docker Networking](../06-networking/README.md) | [Bài 08 - Docker Compose](../08-docker-compose/README.md) →
