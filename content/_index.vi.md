+++  
title = "Persistent Storage on Amazon EKS"  
date = 2024  
weight = 1  
chapter = false  
+++

# Persistent Storage on Amazon EKS

#### Tổng quan

![architecture](/images/1-introduction/1.1.png)

Trong workshop này, bạn sẽ học cách tích hợp hai dịch vụ lưu trữ AWS, **Amazon EBS** và **Amazon EFS**, làm giải pháp lưu trữ bền vững cho cụm **Amazon EKS** của bạn. Các dịch vụ này cung cấp nền tảng cho các giải pháp lưu trữ có thể mở rộng, tin cậy và bền vững dành cho các ứng dụng container hóa.

#### Amazon EBS

**Amazon Elastic Block Store (EBS)** là một dịch vụ lưu trữ khối hiệu suất cao, được tối ưu hóa cho các workload đòi hỏi thông lượng lớn và cường độ giao dịch cao. Dịch vụ này cho phép gắn các volume lưu trữ vào instance EC2 và các pod Kubernetes, cung cấp lưu trữ bền vững cho các ứng dụng xử lý dữ liệu chuyên sâu.

#### Amazon EFS

**Amazon Elastic File System (EFS)** là một hệ thống file được quản lý hoàn toàn, linh hoạt và có khả năng mở rộng. Nó lý tưởng cho các workload như phân tích dữ liệu lớn, phục vụ web, quản lý nội dung, phát triển và kiểm thử, cùng với các quy trình làm việc liên quan đến media. EFS tự động sao chép dữ liệu trên nhiều **Availability Zone** để đảm bảo độ sẵn sàng cao.

{{% notice note %}}  
Để cấu hình lưu trữ tối ưu cho cụm EKS của bạn, điều quan trọng là phải hiểu các khái niệm Kubernetes như Persistent Volumes (PV), Persistent Volume Claims (PVC), và Storage Classes. Workshop này sẽ hướng dẫn bạn từng bước qua các khái niệm này.  
{{% /notice %}}

#### Các khái niệm lưu trữ trong Kubernetes

- **Persistent Volumes (PV):** Một tài nguyên lưu trữ trong Kubernetes được tạo thủ công hoặc tự động. PV có vòng đời độc lập với bất kỳ pod nào.
- **Persistent Volume Claims (PVC):** Một yêu cầu lưu trữ của người dùng, liên kết với một PV. PVC chỉ định dung lượng lưu trữ và các chế độ truy cập (e.g., ReadWriteOnce, ReadOnlyMany, ReadWriteMany).
- **Storage Classes:** Định nghĩa các loại lưu trữ khác nhau, thường liên quan đến các mức độ chất lượng dịch vụ hoặc chính sách sao lưu.
- **Dynamic Volume Provisioning:** Tự động cung cấp lưu trữ theo yêu cầu của người dùng, loại bỏ việc phải thiết lập thủ công bởi quản trị viên.

#### Nội dung Workshop

Workshop này tập trung vào việc tích hợp **Amazon EBS** và **Amazon EFS** với cụm EKS thông qua:

- **Static Provisioning:** Cấu hình lưu trữ thủ công.
- **Dynamic Provisioning:** Lưu trữ được cung cấp tự động dựa trên yêu cầu của người dùng.

#### Các phần của Workshop

1. [Giới thiệu về Amazon EBS và EFS](1-introduction/)
2. [Chuẩn bị](2-prerequisites/)
3. [EKS với Amazon EBS](3-ebs/)
4. [EKS với Amazon EFS](4-efs/)
5. [Dọn dẹp tài nguyên](5-cleanup/)
