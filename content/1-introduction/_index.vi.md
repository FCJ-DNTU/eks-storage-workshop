+++
title = "1.Giới thiệu"
date = 2024
weight = 1
chapter = false
+++

Trong phần này, chúng ta sẽ giới thiệu về khái niệm lưu trữ bền vững trên Amazon Elastic Kubernetes Service (EKS). Lưu trữ bền vững rất quan trọng đối với các ứng dụng Kubernetes cần lưu trữ dữ liệu vượt qua vòng đời của các container hoặc pod riêng lẻ. Chúng ta sẽ tập trung vào hai dịch vụ AWS tích hợp liền mạch với EKS để cung cấp các giải pháp lưu trữ mở rộng và đáng tin cậy: **Amazon Elastic Block Store (EBS)** và **Amazon Elastic File System (EFS)**.

#### Amazon EBS (Elastic Block Store)

Amazon EBS là dịch vụ lưu trữ khối hiệu suất cao, tối ưu hóa cho các khối lượng công việc yêu cầu thông lượng cao và độ trễ thấp. Nó lý tưởng cho các ứng dụng cần lưu trữ dữ liệu bền vững, bao gồm cơ sở dữ liệu và các ứng dụng hiệu suất cao. Các volume EBS có thể được gắn vào các phiên bản EC2 và các pods Kubernetes, cung cấp giải pháp lưu trữ nhất quán và có thể mở rộng.

- **Hỗ trợ bởi:** EC2 Instances
- **Các trường hợp sử dụng:** Cơ sở dữ liệu hiệu suất cao, ứng dụng giao dịch và lưu trữ bền vững cho các ứng dụng Kubernetes.

#### Amazon EFS (Elastic File System)

Amazon EFS là dịch vụ lưu trữ tệp hoàn toàn được quản lý và có thể mở rộng, hỗ trợ cả EC2 và Fargate. Nó lý tưởng cho các trường hợp sử dụng như phân tích dữ liệu lớn, quản lý nội dung, phục vụ web và sao lưu. EFS cung cấp quyền truy cập tệp với độ trễ thấp, lý tưởng cho các ứng dụng yêu cầu lưu trữ tệp bền vững, chia sẻ giữa các pods Kubernetes ở nhiều Vùng Sẵn sàng khác nhau.

- **Hỗ trợ bởi:** EC2 Instances và Fargate
- **Các trường hợp sử dụng:** Lưu trữ tệp cho các ứng dụng yêu cầu truy cập với độ trễ thấp, bao gồm hệ thống quản lý nội dung, phân tích dữ liệu và máy chủ web.

---

#### Khái niệm Lưu trữ trong Kubernetes

Để tích hợp lưu trữ bền vững vào EKS thành công, điều quan trọng là phải hiểu các khái niệm lưu trữ chính trong Kubernetes:

- **Persistent Volumes (PV):** Là tài nguyên lưu trữ trong Kubernetes tồn tại độc lập với vòng đời của pod. PV có thể được cấp phép tĩnh hoặc động bởi người quản trị và đại diện cho cơ sở hạ tầng lưu trữ cơ bản.
- **Persistent Volume Claims (PVC):** Là yêu cầu lưu trữ từ người dùng hoặc ứng dụng, liên kết với một PV cụ thể. PVC chỉ định kích thước và chế độ truy cập yêu cầu (ví dụ: ReadWriteOnce, ReadOnlyMany).
- **Storage Classes:** Định nghĩa các loại lưu trữ khác nhau, thường liên kết với các mức chất lượng dịch vụ hoặc chính sách sao lưu. Kubernetes không quy định Storage Class đại diện cho điều gì, nhưng cho phép tùy chỉnh dựa trên nhu cầu của ứng dụng.
- **Dynamic Provisioning:** Tính năng cho phép tự động cấp phát lưu trữ khi có yêu cầu, loại bỏ sự can thiệp thủ công từ người quản trị.

Trong hội thảo này, chúng ta sẽ tìm hiểu cách cấu hình **Amazon EBS** và **Amazon EFS** cho lưu trữ bền vững trong EKS thông qua cả hai phương pháp cấp phát tĩnh và cấp phát động.
