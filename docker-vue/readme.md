# Dockerize Vue 3 + Vite

_Now using node v18.20.2_

This template should help get you started developing with Vue 3 in Vite. The template uses Vue 3 `<script setup>` SFCs, check out the [script setup docs](https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup) to learn more.

## Recommended IDE Setup

- [VS Code](https://code.visualstudio.com/) + [Vue - Official](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (previously Volar) and disable Vetur

#### Cấu hình Dockerfile

```
# build stage
FROM node:16-alpine as build-stage
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build
## các bạn có thể dùng yarn install .... tuỳ nhu cầu nhé

# production stage
FROM nginx:1.17-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
CMD ["nginx", "-g", "daemon off;"]
```

- Ở file Dockerfile chúng ta chia làm 2 stage (giai đoạn) khi build image: build stage và production stage
- Ở `build stage`ta bắt đầu từ image tên là `node:16-alpine`. Để đặt tên cho từng giai đoạn ta dùng từ khoá `as` nhé ( as build-stage )
- Trong `build stage` ta bắt đầu từ đường dẫn `/app`, sau đó copy toàn bộ file ở folder hiện tại ở môi trường ngoài, tức folder docker-vue vào bên trong đường dẫn ta set ở WORKDIR tức `/app `bên trong image.
- Tiếp theo ta chạy `npm install` như thường lệ để cài dependencies và cuối cùng là build project
  Ok build xong giờ tiến tới `production stage` nơi ta định nghĩa cách chạy project
- Ở `production stage` ta bắt đầu với image tên là `nginx`.... đặt tên stage này là production-stage với từ khoá as

- Thế nginx ở đây là cái gì thế ???? Project VueJS hay ReactJS khi chạy sẽ cần một `webserver` để có thể chạy được nó, và Nginx ở đây chính là `webserver`. Ở local có cần Nginx gì đâu nhỉ? Vì ở local khi chạy npm run dev thì các nhà phát triển VueJS đã thiết lập sẵn cho chúng ta 1 local `webserver` rồi nhé, nhưng khi chạy thật thì không nên dùng nhé, phải có 1 `webserver` xịn, và Nginx thì rất là xịn nhé 😄 😄
- Sau đó, phần này quan trọng nè, ta COPY từ `build-stage` lấy folder ở đường dẫn `app/dist` chính là "những file build cuối cùng cần thiết để chạy ở trình duyệt", ta lấy folder đó và copy vào đường dẫn /`usr/share/nginx/html`, đây chính là nơi Nginx sẽ tìm tới và trả về cho user khi user truy cập ở trình duyệt
- Và cuối cùng ta có `CMD` khởi động Nginx

#### Tạo .dockerignore

```
node_modules
dist
```

- Ở trên ta nói với Docker rằng đừng có copy folder `node_modules` (nếu có) trong quá trình build image nhé, bởi vì làm như vậy image của ta sẽ rất nặng và có thể xảy ra lỗi Canceled: context canceled lúc build

#### Build image

```
docker build -t learning-docker/docker-vue:v1 .
```

#### Chạy project

Để chạy project ta lại "rất như thường lệ các bài trước" lại tại một file tên là docker-compose.yml với nội dung như sau:

```
version: "3.7"

services:
  app:
    image: learning-docker/vue:v1
    ports:
      - "5000:80"
    restart: unless-stopped

```

- Những thứ bên trên cũng được mình đã giải thích ở các bài trước rồi nhé các bạn 😉, Lại "rất như thường lệ" nếu các bạn không hiểu thì các bạn xem lại các bài trước nhé 😄 😄

- "TỪ! Ở trên 80 lấy ở đâu ra vậy anh zai viết blog hê"

- Thì mặc định của Nginx khi bạn ý chạy thì bạn ý sẽ lắng nghe ở cổng 80 nhé các bạn 😉 , dù trong Dockerfile ta không có, cái đó được setup ở trong Nginx rồi nhé ta chưa cần quan tâm.

- Cuối cùng là chạy project lên thôi nào. Lại "rất như thườ....." (thôi không nói nữa), ta chạy command sau:

```
docker compose up -d
```
