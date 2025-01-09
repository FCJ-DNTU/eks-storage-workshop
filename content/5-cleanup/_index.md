
+++  
title = "Clean up resources"  
date = 2024  
weight = 5 
chapter = false  
pre = "5."  
+++
### Delete EKS Cluster

1. Execute the following command to delete the EKS cluster.

```
eksctl delete cluster --name fcj-storage-cluster --region ap-southeast-1
```

![clean](/images/5-cleanup/5.1.png)

{{% notice info %}}
It will take about 20 minutes to complete the deletion

{{% /notice %}}

![clean](/images/5-cleanup/5.2.png)
![clean](/images/5-cleanup/5.3.png)

### Deleting EC2

1. Go to EC2

2. Select **FCJ-Workspace**, select **Action**
3. Click **Terminate instance**.

![clean](/images/5-cleanup/5.5.png)

4. Enter `Delete` to confirm. 5. Click **Delete**.

### Delete IAM Role

1. Delete the IAM Role named **AmazonEKS_EFS_CSI_DriverRole**.

![clean](/images/5-cleanup/5.6.png)

![clean](/images/5-cleanup/5.7.png)
