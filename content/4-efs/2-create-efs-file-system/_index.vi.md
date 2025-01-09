+++  
title = "Tạo EFS File System"  
date = 2024  
weight = 2  
chapter = false  
pre = "4.2"  
+++

Amazon EFS CSI driver hổ trợ cung cấp động và tĩnh. Hiện tại, Cung cấp động tạo một điểm truy cập cho mỗi PV. Điều này có nghĩa là một Amazon EFS phải được tạo thủ công trên AWS trước tiên và nên được cung cấp như dữ liệu đầu vào cho thông số của Storage Class. Với cung cấp tĩnh, Amazon EFS cần được tạo thử công trên AWS trước. Sau đó, nó có thể được gắn vào bên trong một container như là một ổ đĩa sử dụng trình điều khiển.

### Tạo Security Group

1. Truy xuất ID của VPC chứa cụm của bạn và lưu trữ nó trong một biến để sử dụng ở bước sau.

```
vpc_id=$(aws eks describe-cluster \
    --name fcj-storage-cluster \
    --query "cluster.resourcesVpcConfig.vpcId" \
    --output text)
echo $vpc_id
```

2. Truy xuất phạm vi CIDR cho VPC của cụm của bạn và lưu trữ nó trong một biến để sử dụng ở bước sau.

```
cidr_range=$(aws ec2 describe-vpcs \
    --vpc-ids $vpc_id \
    --query "Vpcs[].CidrBlock" \
    --output text \
    --region ap-southeast-1)
echo $cidr_range
```

![create-efs](/images/4-efs/4.2.1.png)

Bây giờ chúng ta sẽ tạo một Security Group với quy tắc đầu vào cho phép truy cập đầu vào với lưu lượng NFS cho EFS của bạn.

3. Tạo một Security Group.

```
security_group_id=$(aws ec2 create-security-group \
    --group-name MyEfsSecurityGroup \
    --description "My EFS security group" \
    --vpc-id $vpc_id \
    --output text)
```

4. Tạo một quy tắc đầu vào mà cho phép lưu lượng đầu vào NFS từ CIDR của VPC Cluster.

```
aws ec2 authorize-security-group-ingress \
    --group-id $security_group_id \
    --protocol tcp \
    --port 2049 \
    --cidr $cidr_range
```

![create-efs](/images/4-efs/4.2.2.png)

### Tạo EFS File System

1. Tạo một File System.

```
file_system_id=$(aws efs create-file-system \
    --region ap-southeast-1 \
    --performance-mode generalPurpose \
    --query 'FileSystemId' \
    --output text)
echo $file_system_id
```

![create-efs](/images/4-efs/4.2.3.png)

2. Đi đến [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems) để xác minh.

Có một File System được tạo với **File system ID** khớp với giá trị của **file_system_id**.

3. Nhấn vào EFS File System.

![create-efs](/images/4-efs/4.2.4.png)

4.  Chuyển đến thanh chuyển hướng **Network**.

Chưa có **Mount Target** được tạo.
![create-efs](/images/4-efs/4.2.5.png)

5. Xác minh địa chỉ IP của Node.

```
kubectl get nodes
```

6. Xác minh ID của Subnet trong VPC và Availability Zone nào chứa Subnet.

```
aws ec2 describe-subnets \
    --region ap-southeast-1 \
    --filters "Name=vpc-id,Values=$vpc_id" \
    --query 'Subnets[*].{SubnetId: SubnetId,AvailabilityZone: AvailabilityZone,CidrBlock: CidrBlock}' \
    --output table
```

Cluster node với IP là 192.168.18.22 thuộc CIDR Block **192.168.0.0/19** và SubnetId **subnet-0a75ff93096b88a38**.

7. Thêm Mount Target cho Subnet của Node. Thay thế với `subnet-id`.

```
aws efs create-mount-target \
    --file-system-id $file_system_id \
    --subnet-id subnet-0a75ff93096b88a38 \
    --security-groups $security_group_id
```

![create-efs](/images/4-efs/4.2.6.png)

8. Đi đến thanh chuyển hướng **Network** của [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems) để kiểm tra Mount Target.

![create-efs](/images/4-efs/4.2.7.png)

Có một Mount Target mới được tạo.

![create-efs](/images/4-efs/4.2.8.png)

#### Bạn đã vừa tạo EFS File System và Mount Target thành công.
