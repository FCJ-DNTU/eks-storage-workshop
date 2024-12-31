+++
title = "Installing the Amazon EBS CSI Driver"
date = 2024
weight = 1
chapter = false
pre = "3.1."
+++

#### Installing the Amazon EBS CSI Driver

The Amazon Elastic Block Store (Amazon EBS) Container Storage Interface (CSI) driver manages the lifecycle of Amazon EBS volumes as storage for the Kubernetes persistent volumes that you create. The Amazon EBS CSI driver provides Amazon EBS volumes for the following Kubernetes volume types: ephemeral volumes and persistent volumes.

#### Creating an IAM Policy

The Amazon EBS CSI plugin requires IAM permissions to call the AWS API.

- Navigate to **IAM** in the AWS Management Console.

- Select **Policy**.

- Click **Policy**.

![3.1.1](/images/3-ebs/3.1.1.png)

#### Using the Policy Editor

- In the Policy editor, select the **JSON** tab.
- Paste the following JSON code:

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

- Click **Next**.
- Enter **Amazon_EBS_CSI_Driver** as the **Policy Name**.

- Optionally, add a description such as **Policy for EC2 Instances to access Elastic Block Store**.

- Click **Next**.

![3.1.2](/images/3-ebs/3.1.2.png)

- Enter Policy name: **Amazon_EBS_CSI_Driver**
- Enter Policy for EC2 Instances to access Elastic Block Store as Description - optional.

- Click Create policy.

![3.1.3](/images/3-ebs/3.1.3.png)

- Create **Create Policy**

![3.1.4](/images/3-ebs/3.1.4.png)

#### List IAM Roles used by Worker Nodes

```bash
kubectl -n kube-system describe configmap aws-auth
```

![3.1.5](/images/3-ebs/3.1.5.png)

Check the value of rolearn to find the name of the IAM Role. For example: In this case, the IAM Role name is eksctl-fcj-storage-cluster-nodegro-NodeInstanceRole-dN18fcwv9euu.

#### Associate Policy to IAM Role

Go to IAM Role. In the Search feature, enter the name of the IAM Role you just found.

Click on it.

![3.1.6](/images/3-ebs/3.1.6.png)

Click Attach policies of the Add permissions feature.

![3.1.7](/images/3-ebs/3.1.7.png)

- Search for the policy named `Amazon_EBS_CSI_Driver`.

- Select the result you found.

- Click Add permissions.

![3.1.8](/images/3-ebs/3.1.8.png)

### Deploy Amazon EBS CSI Driver

```
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"
kubectl get pods -n kube-system
```

![3.1.9](/images/3-ebs/3.1.9.png)

#### You have successfully installed the Amazon EBS CSI Driver, move on to the next step to demonstrate how to use this driver to deploy EBS Storage to an Amazon EKS Cluster.
