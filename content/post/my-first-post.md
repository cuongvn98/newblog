---
title: "Giới thiệu về Docker"
date: 2021-01-16T23:06:59+07:00
draft: false
tags: ["docker"]
categories: ["devops"]
---

Mình đã suy nghĩ xem nên khai bút bằng chủ đề gì . Nhưng thật là khó để chọn 1 chủ đề để nghiên cứu và viết.
Nên có lẽ mình sẽ bắt đầu bằng công cụ mà mình dùng hằng ngày và mình luôn ưu tiên cài đặt đầu tiên.

Hồi mình bắt đầu tìm hiểu về docker (mid 2018) thì docker thời điểm đó ở Việt Nam vẫn chưa nổi lắm. Nhưng hiện tại thì có thể 
dễ thấy nó đã trở thành xu thế trong ngành.

Dạo một vòng 1 trang tìm việc IT lớn mình thấy hầu hết các công ty hiện tại đều ưu tiên đưa docker vào desired skill
như một ưu tiên hàng đầu cho ứng viên của mình.

![demonstrative image 1](/images/my-first-post/job1.png)

![demonstrative image 2](/images/my-first-post/job2.png)


Vậy tóm lại docker là gì ?

> Docker là một ứng dụng giúp đơn giản hóa quá trình quản lý các tiến trình vào trong conntainer . Containers cho phép bạn
> chạy ứng dụng trong một môi trường riêng biệt .
> Nó như là 1 máy ảo nhưng:
>  - Có tính di động cao
>  - Thân thiện với tài nguyên
>  - Phụ thuộc nhiều vào host OS hơn

Ngoài ra docker còn có những ưu điểm chính như sau : 

#### Lightweight resource utilization:
Thay vì phải ảo hoá toàn bộ hệ điều hành , containers bị cô lập ở tầng process và xử dụng host kernel.
#### Portability:
Toàn bộ dependencies được đóng goí trong container , cho phép nó chạy trên bất kì host nào
#### Predictability:
Host hoàn toàn không cần quan tâm về những gì đang chạy bên trong container và container cũng không quan tâm
về nó đang được chạy trên host nào . 

Không gì là hoàn hảo docker cũng vậy theo mình nó có vài nhược điểm như sau :
#### Platform-dependent
Mặc dù được quảng cáo như hỗ trợ cả Windows, Mac OS X và cả Linux. Nhưng thật sự nó vẫn sử dụng máy ảo khi hoạt 
động trên các nền tảng khác linux.
#### Persistent data
Mặc định toàn bộ data trong container sẽ bị xoá toàn bộ khi container bị xoá đi. Trừ khi bạn lưu nó vào đâu đó trước.
Cách đó là sử dụng Docker Data Volumes.

### Cài đặt
Phần cài đặt mình sẽ hướng dẫn các bạn cài đặt trên Ubuntu 20.04. 

Đầu tiên chúng ta mở terminal lên và bắt đầu cập nhập thông tin về các packages và upgrade hệ thống .
Bước này là tùy chọn nhưng mình vẫn đưa vào để đảm bảo hệ thống luôn up-to-date

```bash
    sudo apt-get update
    sudo apt-get dist-upgrade
```

Tiếp theo là cài đặt dependencies

```bash
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

Thêm GPG key và kiểm tra liệu key có hợp lệ:

```bash
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  sudo apt-key fingerprint 0EBFCD88
  
```
Tiếp đó là cài đặt repository:

```bash
 sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

Chúng ta cập nhật lại thông tin về repository vừa được thêm vào và bắt đầu cài đặt:

```bash
 sudo apt-get update
 sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Sau khi cài đặt thành công tiến hành kiểm tra docker đã hoạt động chưa bằng câu lệnh:
```bash
  sudo docker run hello-world

```

Nếu có được kết quả như hình thì quá trình cài đặt đã thành công!

![hello-world](/images/my-first-post/docker-helloworld.png)


### Bonus
Rất bất tiện khi phải thêm sudo khi bắt đầu mỗi câu lệnh .Bạn có thể giải quyết bằng cách thêm user hiện tại vào docker group như sau:

```bash
 sudo usermod -aG docker <your-user>
```