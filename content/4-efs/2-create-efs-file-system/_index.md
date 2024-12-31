---
title: "Create EFS File System"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 4.2 </b> "
---

The Amazon EFS CSI driver supports both dynamic and static provisioning. Currently, Dynamic Provisioning creates an access point for each PV. This means that an Amazon EFS must be manually created on AWS first and should be provided as input to the Storage Class parameter. With static provisioning, an Amazon EFS must be manually created on AWS first. It can then be mounted inside a container as a volume using the driver.

### Create a Security Group

1. Retrieve the ID of the VPC that contains your cluster and store it in a variable for use in the next step.

```
vpc_id=$(aws eks describe-cluster \
--name fcj-storage-cluster \
--query "cluster.resourcesVpcConfig.vpcId" \
--output text)
echo $vpc_id
```

2. Retrieve the CIDR range for your cluster's VPC and store it in a variable for use in the next step.

```
cidr_range=$(aws ec2 describe-vpcs \
--vpc-ids $vpc_id \
--query "Vpcs[].CidrBlock" \
--output text \
--region ap-southeast-1)
echo $cidr_range
```

![create-efs](/images/4-efs/4.2.1.png)

Now we will create a Security Group with an ingress rule that allows ingress access with NFS traffic to your EFS.

3. Create a Security Group.

```
security_group_id=$(aws ec2 create-security-group \
--group-name MyEfsSecurityGroup \
--description "My EFS security group" \
--vpc-id $vpc_id \
--output text)
```

4. Create an ingress rule that allows NFS ingress traffic from the CIDR of the VPC Cluster.

```
aws ec2 authorize-security-group-ingress \
--group-id $security_group_id \
--protocol tcp \
--port 2049 \
--cidr $cidr_range
```

![create-efs](/images/4-efs/4.2.2.png)

### Create an EFS File System

1. Create a File System.

```
file_system_id=$(aws efs create-file-system \
--region ap-southeast-1 \
--performance-mode generalPurpose \
--query 'FileSystemId' \
--output text)
echo $file_system_id
```

![create-efs](/images/4-efs/4.2.3.png)

2. Go to [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems) to verify.

There is a File System created with **File system ID** matching the value of **file_system_id**.

3. Click on EFS File System.

![create-efs](/images/4-efs/4.2.4.png)

4. Go to the **Network** navigation bar.

No **Mount Target** created.
![create-efs](/images/4-efs/4.2.5.png)

5. Verify the Node IP address.

```
kubectl get nodes
```

6. Verify the Subnet ID in the VPC and which Availability Zone contains the Subnet.

```
aws ec2 describe-subnets\
 --region ap-southeast-1 \
 --filters "Name=vpc-id,Values=$vpc_id" \
 --query 'Subnets[*].{SubnetId: SubnetId,AvailabilityZone: AvailabilityZone,CidrBlock: CidrBlock}' \
 --output table
```

Cluster node with IP 192.168.18.22 belongs to CIDR Block **192.168.0.0/19** and SubnetId **subnet-0a75ff93096b88a38**.

7. Add Mount Target for Node Subnet. Replace with `subnet-id`.

```
aws efs create-mount-target \
--file-system-id $file_system_id \
--subnet-id subnet-0a75ff93096b88a38 \
--security-groups $security_group_id
```

![create-efs](/images/4-efs/4.2.6.png)

8. Go to the [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems) **Network** navigation bar to check the Mount Target.

![create-efs](/images/4-efs/4.2.7.png)

A new Mount Target has been created.

![create-efs](/images/4-efs/4.2.8.png)

#### You have just successfully created the EFS File System and Mount Target.
