+++
title = "Bộ lưu trữ cố định Amazon EKS với Amazon EFS"
date = 2024
weight = 4
chapter = false
pre = "4."
+++

### Overview

![architecture](/images/4-efs/architecture.png)

Amazon Elastic File System là một hệ thống tệp đàn hồi đơn giản, không có máy chủ, có thể cài đặt và quên để sử dụng với các dịch vụ Đám mây AWS và tài nguyên tại chỗ. Nó được xây dựng để mở rộng quy mô theo yêu cầu lên tới petabyte mà không làm gián đoạn ứng dụng, tự động tăng và thu nhỏ khi bạn thêm và xóa tệp, loại bỏ nhu cầu cung cấp và quản lý dung lượng để đáp ứng sự tăng trưởng.

### Nội dung

1.  [Cài đặt EFS CSI Driver](1-install-efs-csi)
2.  [Tạo EFS file system](2-create-efs-file-system)
3.  [Cung cấp tĩnh](3-static-provisioning)
4.  [Cung cấp động](4-dynamic-provisioning)
