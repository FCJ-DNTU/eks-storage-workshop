---
title: "Dynamic Provisioning"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4.4 </b> "
---

### Creating Manifest Files

1. Create a directory for the **Dynamic Provisioning** section.

```
mkdir -p EFS/dynamic-provision/kube-manifest
ls EFS/dynamic-provision
```

2. Create a file named **storageclass.yaml** inside **dynamic-provision/kube-manifest**.

```
touch EFS/dynamic-provision/kube-manifest/storageclass.yaml
```

3. Open the **storageclass.yaml** file, paste the code below. Replace with **EFS File System ID** in the **fileSystemId** field.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
 name: efs-sc
provisioner: efs.csi.aws.com
parameters:
 provisioningMode: efs-ap
 fileSystemId: fs-0db5cdf65fc6c821d
 directoryPerms: "700"
```

![dynamic](/images/4-efs/4.4.1.png)

4. Create a file named **pvc.yaml** inside **dynamic-provision/kube-manifest**.

```
touch EFS/dynamic-provision/kube-manifest/pvc.yaml
```

5. Open the file **pvc.yaml**, paste the code below.

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: efs-claim
spec:
 accessModes:
 - ReadWriteMany
 storageClassName: efs-sc
 resources:
 requests:
 storage: 5Gi
```

![dynamic](/images/4-efs/4.4.2.png)

6. Create a file named **pod.yaml** inside **dynamic-provision/kube-manifest**.

```
touch EFS/dynamic-provision/kube-manifest/pod.yaml
```

7. Open the **pod.yaml** file, paste the code below.

```
apiVersion: v1
kind: Pod
metadata:
 name: efs-app
spec:
 containers:
 - name: app
 image: centos
 command: ["/bin/sh"]
 args: ["-c", "while true; do echo $(date -u) >> /data/out; sleep 5; done"]
 volumeMounts:
 - name: persistent-storage
 mountPath: /data
 volumes:
 - name: persistent-storage
 persistentVolumeClaim:
 claimName: efs-claim
```

![dynamic](/images/4-efs/4.4.3.png)

### Deploy resources

1. Execute the command below to deploy the resource.

```
kubectl apply -f EFS/dynamic-provision/kube-manifest
```

2. List all the resources that have been created.

```
kubectl get sc,pvc,pod
```

![static-provisition](/images/4-efs/4.4.4.png)

{{% notice important %}}
**STATUS** of **Persistent Volume Claim (pvc)** should be **Bound**, and **Pod** should be **Running**.

{{% /notice %}}

3. Navigate to [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems).
4. Click on your EFS File System.
5. Switch to the **Access points** navigation bar.

![static-provisition](/images/4-efs/4.4.5.png)

You can see that an **Access point** has just been created because you specified **parameters.provisioningMode** as **efs-ap** in the **storageclass.yaml** file.

### Check the result.

1. Execute the following command to verify that the data has been written to the EFS filesystem.

```
kubectl exec efs-app -- bash -c "cat data/out"
```

![static-provisition](/images/4-efs/4.4.6.png)

There is a lot of data written to the **/data/out** file. Save the contents of the first three for later comparison.

2. Now we will delete the current Pod. Then list all the resources again to make sure the Pod has been deleted.

```
kubectl delete pod efs-app
kubectl get sc,pvc,pod
```

![static-provisition](/images/4-efs/4.4.7.png)

Pod **efs-app** has been deleted.

3. Create a new Pod.

```
kubectl apply -f EFS/dynamic-provision/kube-manifest/pod.yaml
kubectl get pod
```

![static-provisition](/images/4-efs/4.4.7.png)

4. Verify the data inside **/data/out** again.

```
kubectl exec efs-app -- bash -c "cat data/out"
```

![static-provisition](/images/4-efs/4.4.8.png)

You can see, the first three data. The data is kept while the Pod is replaced with another one.

### Cleanup

1. Execute the command below to delete all resources.

```
kubectl delete -f EFS/dynamic-provision/kube-manifest
```

```
kubectl get sc,pvc,pod
```

![static-provisition](/images/4-efs/4.4.9.png)

3. Navigate to [EFS](https://ap-southeast-1.console.aws.amazon.com/efs/home?region=ap-southeast-1#/file-systems).
4. Select your EFS File System.
5. Click **Delete**.

![static-provisition](/images/4-efs/4.4.10.png)

6. Enter the EFS File System ID to confirm.
7. Click **Confirm** to delete.

![static-provisition](/images/4-efs/4.4.11.png)
