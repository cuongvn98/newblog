---
title: "Dockerize node.js app"
date: 2021-01-25T20:45:40+07:00
draft: false
tags: ["docker", "node.js"]
categories: ["devops"]
---

Ở bài trước mình đã bàn qua về docker và cài đặt docker trên ubuntu . Suốt tuần qua mình cũng đắn đo xem nên viết gì ở tuần này 
. Thú thật cũng có vài ý tưởng, cũng muốn viết 1 bài chuyên sâu. Nhưng suy nghĩ kĩ thì mình sẽ tiếp tục với 1 bài cơ bản về docker trước khi 
viết gì đó cao siêu hơn. Tiếp tục với docker cơ bản thì hôm nay mình sẽ hướng dẫn đóng gói 1 image cho node.js application.

### Chuẩn bị :
* Docker.
* Node.js 
* IDE hoặc text editor tuỳ thích .

Nếu các bạn sử dụng linux , mac thì mình khuyên các bạn nên dùng [nvm](https://github.com/nvm-sh/nvm) để dễ dàng quản lý node version.

### Node.js app

Về application thì chúng ta sẽ viết một web service đơn giản trong đó nó sẽ trả về thông tin của ip của client đang gửi request.


Chúng ta sẽ bắt đầu như sau :

Đầu tiên chúng ta khởi tạo project với chuỗi lệnh:

```bash
mkdir dockerize_node_app && cd $_
npm init -y
```

Và cài đặt express.js như là dependency :

```bash
npm i express
```

Sau khi chuẩn bị khởi tạo xong project và cài đặt deps cần thiết thì chúng ta tạo file index.js với nội dung :

```node.js
const express = require("express");
const app = express();

app.get("/", function (req, res) {
  const ip = req.headers["x-forwarded-for"] || req.socket.remoteAddress;
  res.json({ ip: ip });
});

app.listen("8080", () => {
  console.log("server is listening on :8080");
});
```

Như bạn có thể thấy ứng dụng của chúng ta đơn thuần chỉ khởi tạo 1 web service bất cứ request nào tới đường dẫn / đều 
sẽ trả về ip của client . Không có gì phức tạp cả vậy nên sau khi dùng lệnh 

```bash
node index
```

Và mở browser với địa chỉ localhost:8080 thì ta sẽ nhận được kết quả như sau :

![run node app](/images/dockerize_node_app/run_node_app.png)

Nhưng ip trả về cũng có thể là 127.0.0.1 (ipv4) .


Vậy tiếp theo chúng ta sẽ đến với phần chính. Dockerize nó.

### Dockerize

Để bắt đầu chúng ta sẽ tạo 1 file .dockerignore với nội dung như sau:

> node_modules

Nhằm mục đích rằng khi build docker image thì docker daemon sẽ bỏ qua thư mục node_modules thay vì copy nó vào như là build context.
Tiếp đó ta sẽ tạo file Dockerfile với nội dung như sau:

```Dockerfile
FROM node:15.6.0-alpine3.10

WORKDIR /usr/app

COPY package-*.json .

RUN npm i

COPY . .

EXPOSE 8080

CMD  ["node", "index"]
```

Mình sẽ giải thích từng dòng như sau :

Đầu tiên khi khởi tạo 1 image nào thì chúng ta cũng chọn image cơ bản mà ứng dụng chúng ta build từ .
Chúng ta hoàn toàn có thể build từ image trống như là scratch. Nhưng trong nội dung bài này chúng ta sẽ dùng 1 image node.js được
build sẵn đó là node:15.6.0-alpine3.10

```Dockerfile
FROM node:15.6.0-alpine3.10
```

Sau đó chúng ta sẽ chỉ định thư mục mà chúng ta sẽ làm việc với lệnh 

```Dockerfile
WORKDIR /usr/app
```

Trong image của chúng ta nó sẽ là nơi lưu trữ code. Sau đó chúng ta sẽ copy 2 file package.json và package-lock.json 
vào và tiến hành cài dependencies .

```Dockerfile
COPY package-*.json .

RUN npm i
```

Sau khi cài xong thì ta sẽ bắt đầu copy toàn bộ dự án vào trong image với lệnh COPY

```Dockerfile
COPY . .
```

Nhưng đến đây chắc các bạn sẽ thắc mắc ủa sao không copy ngay toàn bộ dự án vào ngay từ đầu rồi install deps mà lại phải 
copy 2 file package.json và package-lock.json vào trước làm chi . Lí do để giải thích cho việc này 
là khi build docker daemon sẽ sử dụng cache để tối ưu thời gian build các layer không bị thay đổi giữa các lần build. Từ 
đó giúp tối ưu thời gian build ứng dụng. Như trong service của chúng ta thì việc cập nhật deps xảy ra không thương xuyên thay vào đó là 
source code . 

Và lệnh EXPOSE 8080 chỉ cho docker biết rằng ứng dụng của chúng ta sử dụng port 8080 . 

Lưu ý rằng lệnh này không hỗ trợ việc public port 
nào . Chức năng của nó như là tài liệu của người build image cho người run image rằng port nào được dự định sử dụng . 

Và cuôí cùng là 
```Dockerfile
CMD ["node", "index"]
```

Lệnh này được sử dụng như là chỉ ra lệnh nào sẽ được sử dụng để run  ứng dụng . Lưu ý mỗi image chỉ có duy nhất 1 
lệnh CMD . Như trong ứng dụng của chúng ta dùng  CMD ["node", "index"] để run vậy .

Sau khi có được file Dockerfile chúng ta sẽ build image từ file này như sau :
```bash 
docker build -t dockerize_node_app .
```

Ở lần đầu run thì sẽ mất chút thời gian để pull image node.js về nhưng các lần sau sẽ nhanh hơn . Sau khi build thành công ta nhận được kết quả như sau :

![build_image](/images/dockerize_node_app/build_image.png)


Tiếp đó chúng ta thử tạo container từ image vưà tạo để xem chúng ta đã thành công hay chưa :

```bash
docker run -p 8080:8080 dockerize_node_app
```

Sau đó thử lại với trình duyệt ta nhận được kết quả sau :

![build_image](/images/dockerize_node_app/run_node_app_from_image.png)

Giải thích về lệnh trên như sau : 
với docker run <image_name> ta sẽ tạo 1 container từ 1 image có sẵn và khởi chạy nó . Kèm theo đó vì service của chúng 
ta lắng nghe ở port 8080 nên ta sẽ dùng -p 8080:8080 để bind port 8080 từ host tới port 8080 trong container được tạo .

### Kết luận
Thế là chúng ta đã build và đóng gói thành công 1 node.js application vào docker image. Vẫn còn rất nhiều phần để giúp chúng 
ta deploy hoàn chỉnh 1 ứng dụng. Nhưng mình mong bài viết này sẽ giúp các bạn tiến 1 bước trong việc học hỏi và sử dụng docker hơn .