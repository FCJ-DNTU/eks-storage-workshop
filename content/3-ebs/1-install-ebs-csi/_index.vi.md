+++  
title = "Installing the Amazon EBS CSI Driver"  
date = 2024  
weight = 1
chapter = false  
pre = "3.1."  
+++

#### Cài đặt Trình điều khiển Amazon EBS CSI

Trình điều khiển Giao diện lưu trữ vùng chứa (CSI) của Amazon Elastic Block Store (Amazon EBS) quản lý vòng đời của các ổ đĩa Amazon EBS dưới dạng bộ lưu trữ cho các ổ đĩa liên tục Kubernetes mà bạn tạo. Trình điều khiển Amazon EBS CSI cung cấp các ổ đĩa Amazon EBS cho các loại ổ đĩa Kubernetes sau: ổ đĩa tạm thời và ổ đĩa liên tục.

#### Tạo IAM Policy

Plugin Amazon EBS CSI yêu cầu quyền IAM để gọi API AWS.

- Điều hướng đến **IAM** trong Bảng điều khiển quản lý AWS.
- Chọn **Policy**.
- Nhấp vào **Policy**.

![3.1.1](/images/3-ebs/3.1.1.png)

#### Sử dụng Policy Editor

- Trong trình chỉnh sửa Chính sách, chọn tab **JSON**.
- Dán đoạn mã JSON sau:

```bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:CreateSnapshot",
        "ec2:CreateTags",
        "ec2:CreateVolume",
        "ec2:DeleteSnapshot",
        "ec2:DeleteTags",
        "ec2:DeleteVolume",
        "ec2:DescribeInstances",
        "ec2:DescribeSnapshots",
        "ec2:DescribeTags",
        "ec2:DescribeVolumes",
        "ec2:DetachVolume"
      ],
      "Resource": "*"
    }
  ]
}
```

- Bấm vào **Tiếp theo**.
- Nhập **Amazon_EBS_CSI_Driver** làm **Tên chính sách**.
- Tùy ý, thêm mô tả như **Chính sách dành cho Phiên bản EC2 truy cập Elastic Block Store**.
- Bấm vào **Tiếp theo**.

![3.1.2](/images/3-ebs/3.1.2.png)

- Nhập Policy name: **Amazon_EBS_CSI_Driver**
- Nhập Policy for EC2 Instances to access Elastic Block Store là Description - optional.
- Nhấn Create policy.

![3.1.3](/images/3-ebs/3.1.3.png)

- Create **Create Policy**

![3.1.4](/images/3-ebs/3.1.4.png)

#### Liệt kê IAM Role sử dụng bởi Worker Nodes

```bash
kubectl -n kube-system describe configmap aws-auth
```

![3.1.5](/images/3-ebs/3.1.5.png)

Kiếm tra giá trị của rolearn để tìm kiếm tên của IAM Role. Ví dụ: Trong trường hợp này, tên IAM Role là eksctl-fcj-storage-cluster-nodegro-NodeInstanceRole-dN18fcwv9euu.

#### Liên kết Policy vào IAM Role

Đi đến IAM Role. Tại tính năng Search, nhập tên IAM Role vừa tìm thấy.
Nhấn vào nó.

![3.1.6](/images/3-ebs/3.1.6.png)

Nhấn Attach policies của tính năng Add permissions.

![3.1.7](/images/3-ebs/3.1.7.png)

- Tìm kiếm policy tên `Amazon_EBS_CSI_Driver`.
- Chọn kết quả tìm được.
- Nhấn Add permissions.

![3.1.8](/images/3-ebs/3.1.8.png)

### Deploy Amazon EBS CSI Driver

```
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"
kubectl get pods -n kube-system
```

![3.1.9](/images/3-ebs/3.1.9.png)

#### Bạn đã cài đặt thành công Trình điều khiển Amazon EBS CSI, hãy chuyển sang bước tiếp theo để minh họa cách sử dụng trình điều khiển này để triển khai Bộ lưu trữ EBS cho Cụm Amazon EKS.
