+++
title = "Tạo EKS Cluster"
date = 2024
weight = 4
chapter = false
pre = "2.4."
+++

#### Tạo Amazon EKS Cluster

- Ở terminal của VScode, copy và dán lệnh

```bash
eksctl create cluster --name=fcj-storage-cluster --region=ap-southeast-1 --zones=ap-southeast-1a,ap-southeast-1b --without-nodegroup
```

![2.4.1](/images/2-prerequisites/2.4.2.png)

- Kiểm tra cluster vừa tạo

```bash
eksctl get cluster --region=ap-southeast-1
```

![2.4.2](/images/2-prerequisites/2.4.3.png)

-Cho phép kubectl giao tiếp với cụm của bạn bằng cách thêm context vào tệp cấu hình kubectl.

```bash
aws eks update-kubeconfig --region=ap-southeast-1 --name=fcj-storage-cluster
```

![2.4.3](/images/2-prerequisites/2.4.4.png)

#### Tạo và liên kết cấp IAM OIDC cho Cụm EKS

```bash
eksctl utils associate-iam-oidc-provider --cluster=fcj-storage-cluster --region=ap-southeast-1 --approve
```

![2.4.4](/images/2-prerequisites/2.4.5.png)

- Kiểm tra trong IAM Identity providers

![2.4.5](/images/2-prerequisites/2.4.6.png)

#### Tạo Amazon EKS quản lý Node Group

- Tạo node group, ở tiến trình này bạn phải chờ khoảng 10 phút

```bash
eksctl create nodegroup --name=fcj-storage-nodegroup --cluster=fcj-storage-cluster --region=ap-southeast-1 --node-type=t3.medium --nodes=1
kubectl get nodes

```

![2.4.6](/images/2-prerequisites/2.4.7.png)
