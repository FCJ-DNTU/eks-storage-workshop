+++
title = "Chuẩn bị môi trường"
date = 2024
weight = 1
chapter = false
pre = "2.1."
+++

### Cấu hình EC2

Ở bước này chúng ta tiến hành cấu hình EC2. Ở giao diện console, tìm và chọn **EC2**

- Chọn **Launch Instance**

![2.1.1](/images/2-prerequisites/2.1.1.png)

- Name : `FCJ-Workspace`
- Chọn hệ điều hành: **Ubuntu**

![2.1.2](/images/2-prerequisites/2.1.2.png)

- Chọn Machine Image: **Ubuntu Server 24.04**
- Chọn Instance Type: **t3.large**

![2.1.3](/images/2-prerequisites/2.1.3.png)

- Key pair: **create key pair** nếu bạn chưa có key

![2.1.4](/images/2-prerequisites/2.1.4.png)

- Security Group: bạn có thể chọn security mặc định hoặc là custom một securiety mới, cần thiết phải có SSH port 22
- Chọn **Launch instance** để tạo EC2

![2.1.5](/images/2-prerequisites/2.1.5.png)

#### Connect SSH EC2

- Chọn **Connect**

![2.1.7](/images/2-prerequisites/2.1.7.png)

- Chọn **SSH Client**
- Chọn copy đường dẫn như trên hình

![2.1.8](/images/2-prerequisites/2.1.8.png)

- Mở Visual Studio Code
- Chọn connect ssh và điền thông tin vừa copy

![2.1.9](/images/2-prerequisites/2.1.9.png)

- Chỉnh sửa thông tin đường dẫn key và nhấn connect

![2.1.10](/images/2-prerequisites/2.1.10.png)
