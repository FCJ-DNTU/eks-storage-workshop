---
title: "Install Amazon EFS CSI Driver"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 4.1 </b> "
---

### Create IAM Role

The Amazon EFS CSI driver requires IAM permissions to interact with your file system. Create an IAM role and attach the required AWS managed policies to it.

1. Execute the following command to create the IAM role.

```
export cluster_name=fcj-storage-cluster
export role_name=AmazonEKS_EFS_CSI_DriverRole
eksctl create iamserviceaccount \
 --name efs-csi-controller-sa \
 --namespace kube-system \
 --cluster $cluster_name \
 --region ap-southeast-1 \
 --role-name $role_name \
 --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEFSCSIDriverPolicy \
 --override-existing-serviceaccounts \
 --approve
TRUST_POLICY=$(aws iam get-role --role-name $role_name --query 'Role.AssumeRolePolicyDocument' | \
 sed -e 's/efs-csi-controller-sa/efs-csi-*/' -e 's/StringEquals/StringLike/')
aws iam update-assume-role-policy --role-name $role_name --policy-document "$TRUST_POLICY"
```

Verify the created Service Account. 2. Execute the following command to check the Service Account named `efs-csi-controller-sa` in the `kube-system` namespace.

```
kubectl get sa efs-csi-controller-sa -n kube-system
```

![Install CSI Driver](/images/4-efs/4.1.1.png)

Verify the created IAM Role.

3. Go to [IAM Role](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/roles).
4. Enter the IAM Role name `AmazonEKS_EFS_CSI_DriverRole` in the **Search** feature.
5. Click IAM Role **AmazonEKS_EFS_CSI_DriverRole**.

![Install CSI Driver](/images/4-efs/4.1.2.png)

6. There is a Policy named **AmazonEFSCSIDriverPolicy** attached to the IAM Role.

![Install CSI Driver](/images/4-efs/4.1.3.png)

7. Go to the **Trust replationships** navigation bar.
8. Verify that there is a condition with the value **system:serviceaccount:kube-system:efs-csi-**.

![Install CSI Driver](/images/4-efs/4.1.4.png)

### Install Amazon EFS CSI driver

1. Check the version of the Cluster.

```
kubectl version
```

The Cluster version is **Server Version** (1.29 not 1.29.3). 2. Check the names of the add-ons available for the Cluster version.

```
eksctl utils describe-addon-versions --region=ap-southeast-1 --kubernetes-version 1.29 | grep AddonName
```

![Install CSI Driver](/images/4-efs/4.1.5.png)

3. Identify the current add-ons and add-on versions installed on your cluster.

```
eksctl get addon --cluster fcj-storage-cluster --region ap-southeast-1
```

4. Get the **Service Account Role ARN** of the **efs-csi-controller-sa** service account.

```
kubectl describe sa efs-csi-controller-sa -n kube-system
```

5. Save the value of **eks.amazonaws.com/role-arn** in the **Annotations** field.
6. Create an Amazon EKS add-on. Replace the specific values ​​in the command **eksctl create addon --cluster <REPLACE_CLUSTER_NAME> --name <REPLACE_ADDON_NAME> --region <REPLACE_REGION_ID> --version latest --service-account-role-arn <REPLACE_IAM_ROLE_ARN> --force**. Then execute it.

```
eksctl create addon --cluster fcj-storage-cluster --name aws-efs-csi-driver --region ap-southeast-1 --version latest \
--service-account-role-arn arn:aws:iam::170074558790:role/AmazonEKS_EFS_CSI_DriverRole --force
```

7. Re-identify the current add-ons and the version of the add-on that has been installed inside the Cluster.

```bash
eksctl get addon --cluster fcj-storage-cluster --region ap-southeast-1

```

![Install CSI Driver](/images/4-efs/4.1.6.png)
