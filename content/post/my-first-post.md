---
title: "Docker"
date: 2021-01-16T23:06:59+07:00
draft: true
---

Mình đã suy nghĩ xem nên khai bút bằng chủ đề gì . Nhưng thật là khó để chọn 1 chủ đề để nghiên cứu và viết.
Nên có lẽ mình sẽ bắt đầu bằng công cụ mà mình dùng hằng ngày và mình luôn ưu tiên cài đặt đầu tiên.

Hồi mình bắt đầu tìm hiểu về docker (mid 2018) thì docker thời điểm ở Việt Nam vẫn chưa nổi lắm. Nhưng hiện tại thì có thể 
dễ thấy nó đã trở thành xu thế trong ngành.

Dạo một vòng 1 trang tìm việc IT lớn mình thấy hầu hết các công ty hiện tại đều ưu tiên đưa docker vào desired skill
như một ưu tiên hàng đầu cho ứng viên của mình.

![demonstrative image 1](/images/my-first-post/job1.png)

![demonstrative image 2](/images/my-first-post/job2.png)


Vậy tóm lại docker là gì ?

> Docker là một ứng dụng giúp đơn giản hóa quá trình quẩn lý các tiến trình vào trong conntainer . Containers cho phép bạn
> chạy ứng dụng trong một môi trường riêng biệt .
> Nó như là 1 máy ảo nhưng:
>  - Có tính di động cao
>  - Thân thiện với tài nguyên
>  - Phụ thuộc nhiều vào host OS hơn

### Tính di động
Với docker bạn có thể đóng gói hoàn toàn ứng dụng của bạn vào trong 1 image cùng với sự hỗ trợ của docker registry 
như là dockerhub v.v. Bạn có thể di chuyển vào chạy ứng dụng của bạn ở mọi nơi miễn là cài đặt docker và image của bạn hỗ trợ 
kiến trúc của host. Cũng như container trong thế giới thực vậy. Nhưng thay vì vận chuyển hàng hóa thì nó vận chuyển ứng dụng.

### Thân thiện với tài nguyên
Thông thường khi khởi tạo máy ảo với virtual box chúng ta phải xác định sẵn số lượng tài nguyên mà máy ảo sử dụng. Nhưng
đôi khi là quá ít và quá nhiều. Có thể gây ra sự lãng phí không đáng có. Nhưng với docker thì các container chạy trực tiếp trên 
host machine như 1 tiến trình . Và khi một tiến trình thì cần bao nhiêu tài nguyên chúng sẽ được cấp phát bằng đó.

### Phụ thuộc nhiều vào host OS hơn 
Phần này mình sẽ chỉ đính kèm 1 bức ảnh khá nổi tiếng . Vì mình nghĩ nói sâu hơn thì chúng ta nên dành ra 1 bài riêng để nói 
về chủ đề này. 

![containers-vs-virtual-machines](/images/my-first-post/containers-vs-virtual-machines.jpg)

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
Rất bất tiện khi phải thêm sudo khi bắt đầu mỗi câu lệnh . Bạn có thể giải quyết bằng cách thêm user hiện tại vào docker group như sau:

```bash
 sudo usermod -aG docker <your-user>
```