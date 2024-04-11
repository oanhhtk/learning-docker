## Cài đăt docker

##### ⚙️ Cấu hình Dockerfile cho nodejs app

```
FROM node:latest

WORKDIR /app

COPY . .

RUN npm install

CMD ["npm", "start"]

```

##### ⚒ Build docker image

```
docker build -t learning-docker/node:v1 .

```

- Option `-t` để chỉ là đặt tên Image là như thế này cho tôi, và sau đó là tên của image.
-

```
docker images
 (show tên image, tag, Mã của Image,....)
```

##### 🏃‍♀️ Chạy project từ Docker Image

```
docker run -d -p 3000:3000 learning-docker/docker-node:v1
```

##### ␡ Xoá image

- Để xoá image thì các bạn dùng command:

```
docker rmi <Mã của Image>
```
