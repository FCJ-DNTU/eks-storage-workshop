---
title: "Static Provisioning"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 4.3 </b> "
---

### Storing current resources.

1. Create a directory named **EBS** to store the resources of the **EBS** section.

```
mkdir EBS
```

2. Move all **dynamic-provision** and **static-provision** directories to the **EBS** directory.

```
mv dynamic-provision EBS
mv static-provision EBS
```

![static-provisition](/images/4-efs/4.3.1.png)

### Creating Manifest files.

1. Create a directory named `EFS` to store the kube-manifest files.

```
mkdir EFS
```

2. Create a directory for the **Static Provisioning** section.

```
mkdir -p EFS/static-provision/kube-manifest
ls EFS/static-provision
```

![static-provisition](/images/4-efs/4.3.2.png)

3. Create a file named **storageclass.yaml** inside **static-provision/kube-manifest**.

```
touch EFS/static-provision/kube-manifest/storageclass.yaml
```

4. Open the **storageclass.yaml** file, paste the code below.

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
name: efs-sc
provisioner: efs.csi.aws.com
```

![static-provisition](/images/4-efs/4.3.3.png)

5. Create a file named **pv.yaml** inside **static-provision/kube-manifest**.

```
touch EFS/static-provision/kube-manifest/pv.yaml
```

6. Open the **pv.yaml** file, paste the code below. Enter your EFS File System ID at **volumeHandle**.

```
apiVersion: v1
kind: PersistentVolume
metadata:
 name: efs-pv
spec:
 capacity:
 storage: 5Gi
 volumeMode: Filesystem
 accessModes:
 - ReadWriteOnce
 storageClassName: efs-sc
 persistentVolumeReclaimPolicy: Retain
 csi:
 driver: efs.csi.aws.com
 volumeHandle: fs-0db5cdf65fc6c821d
```

![static-provision](/images/4-efs/4.3.4.png)

7. Create a file named **pvc.yaml** inside **static-provision/kube-manifest**.

```
touch EFS/static-provision/kube-manifest/pvc.yaml
```

8. Open the file **pvc.yaml**, paste the code below.

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: efs-claim
spec:
 accessModes:
 - ReadWriteOnce
 storageClassName: efs-sc
 resources:
 requests:
 storage: 5Gi
```

![static-provision](/images/4-efs/4.3.5.png)

9. Create a file named **pod.yaml** inside **static-provision/kube-manifest**.

```
touch EFS/static-provision/kube-manifest/pod.yaml
```

10. Open the file **pod.yaml**, paste the code below.

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
args: ["-c", "while true; do echo $(date -u) >> /data/out.txt; sleep 5; done"]
volumeMounts:
- name: persistent-storage
mountPath: /data
volumes:
- name: persistent-storage
persistentVolumeClaim:
claimName: efs-claim
```

![static-provisition](/images/4-efs/4.3.6.png)

### Deploy Resources

1. Execute the command below to deploy the resources.

```
kubectl apply -f EFS/static-provision/kube-manifest
```

2. List all created resources.

```
kubectl get sc,pv,pvc,pod
```

![static-provisition](/images/4-efs/4.3.7.png)

{{% notice important %}}
**STATUS** of **Persistent Volume (pv)** and **Persistent Volume Claim (pvc)** must be **Bound**, and **Pod** must be **Running**.

{{% /notice %}}

### Check the result.

1. Execute the command below to verify that the data has been written to the EFS filesystem.

```
kubectl exec -ti efs-app -- head /data/out.txt
```

![static-provisition](/images/4-efs/4.3.8.png)

There is a lot of data written to the **/data/out.txt** file. Save the contents of the first three data for later comparison.

2. Now we will delete the current Pod. Then list all resources again to make sure the Pod has been deleted.

```
kubectl delete pod efs-app
kubectl get sc,pv,pvc,pod
```

![static-provisition](/images/4-efs/4.3.9.png)

The **efs-app** Pod has been deleted.

3. Create a new Pod.

```
kubectl apply -f EFS/static-provision/kube-manifest/pod.yaml
kubectl get pod
```

4. Verify the data inside **/data/out.txt** again.

```
kubectl exec -ti efs-app -- head /data/out.txt
```

![static-provisition](/images/4-efs/4.3.10.png)

As you can see, the first three data in **/data/out.txt** are still **Wed May 8 11:31:17 UTC 2024**, **Wed May 8 11:31:22 UTC 2024** and **Wed May 8 11:31:27 UTC 2024**. The data is kept while the Pod is replaced with another one.

### Cleanup

1. Execute the command below to delete all resources.

```
kubectl delete -f EFS/static-provision/kube-manifest
```

2. Verify that all resources have been successfully deleted.

```
kubectl get sc,pv,pvc,pod
```

![static-provisition](/images/4-efs/4.3.11.png)

All have been deleted.
