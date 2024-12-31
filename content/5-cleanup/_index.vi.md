---
title: "Dọn dẹp tài nguyên"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

### Xóa EKS Cluster

1. Thực hiện câu lệnh dưới để xóa EKS cluster.

```
eksctl delete cluster --name fcj-storage-cluster --region ap-southeast-1
```

![clean](/images/5-cleanup/5.1.png)

{{% notice info %}}
Sẽ mất khoảng 20 phút để hoàn thành việc xóa
{{% /notice %}}

![clean](/images/5-cleanup/5.2.png)
![clean](/images/5-cleanup/5.3.png)

### Xóa EC2

1. Đi đến EC2
2. Chọn **FCJ-Workspace**, chọn **Action**
3. Nhấn **Terminate instance**.

![clean](/images/5-cleanup/5.5.png)

4. Nhập `Delete` để xác nhận. 5. Nhấn **Delete**.

### Xóa IAM Role

1. Xóa IAM Role tên **AmazonEKS_EFS_CSI_DriverRole**.

![clean](/images/5-cleanup/5.6.png)

![clean](/images/5-cleanup/5.7.png)
