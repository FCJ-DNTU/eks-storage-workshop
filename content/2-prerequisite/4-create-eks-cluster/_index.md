+++
title = "Create EKS Cluster"
date = 2024
weight = 4
chapters = false
pre = "2.4."
+++

#### Create Amazon EKS Cluster

- In VScode terminal, copy and paste the command

```bash
eksctl create cluster --name=fcj-storage-cluster --region=ap-southeast-1 --zones=ap-southeast-1a,ap-southeast-1b --without-nodegroup
```

![2.4.1](/images/2-prerequisites/2.4.2.png)

- Check the newly created cluster

```bash
eksctl get cluster --region=ap-southeast-1
```

![2.4.2](/images/2-prerequisites/2.4.3.png)

-Allow kubectl to communicate with your cluster by adding context to the kubectl configuration file.

```bash
aws eks update-kubeconfig --region=ap-southeast-1 --name=fcj-storage-cluster
```

![2.4.3](/images/2-prerequisites/2.4.4.png)

#### Create and associate IAM OIDC provider for EKS Cluster

```bash
eksctl utils associate-iam-oidc-provider --cluster=fcj-storage-cluster --region=ap-southeast-1 --approve
```

![2.4.4](/images/2-prerequisites/2.4.5.png)

- Check in IAM Identity providers

![2.4.5](/images/2-prerequisites/2.4.6.png)

#### Create Amazon EKS to manage the Node Group

- Create node group, in this process you have to wait about 10 minutes

```bash
eksctl create nodegroup --name=fcj-storage-nodegroup --cluster=fcj-storage-cluster --region=ap-southeast-1 --node-type=t3.medium --nodes=1
kubectl get nodes

```

![2.4.6](/images/2-prerequisites/2.4.7.png)
