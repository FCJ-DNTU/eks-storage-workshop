+++
title = "Tạo IAM Role"
date = 2024
weight = 2
chapter = false
pre = "2.2."
+++

### Tạo IAM Role

Ở phần này chúng ta sẽ thực hiện tạo role administrator cho ec2

- Ở giao diện console tìm và chọn **IAM**
- Chọn **Role**
- Chọn **Create Role**

![2.2.1](/images/2-prerequisites/2.2.1.png)

- Chọn **AWS Service**
- Chọn **EC2**

![2.2.2](/images/2-prerequisites/2.2.2.png)

- Chọn **AdministratorAccess**

![2.2.3](/images/2-prerequisites/2.2.3.png)

- Role name: `eksworkspace-administrator`
- Description: `Allow EC2 instance to call AWS Services on your behalf`

![2.2.4](/images/2-prerequisites/2.2.4.png)

- Chọn **Create role**

![2.2.5](/images/2-prerequisites/2.2.5.png)
