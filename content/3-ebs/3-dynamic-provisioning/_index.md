+++  
title = "Dynamic Provisioning"  
date = 2024  
weight = 3  
chapter = false  
pre = "3.3."  
+++

#### Creating Manifest Files

1. Open the command terminal in VS Code and create a directory named `dynamic-provision/kube-manifest`.

```bash
mkdir -p dynamic-provision/kube-manifest
```

2. Create a file named `storageclass.yaml` inside **dynamic-provision/kube-manifest**.

```bash
touch dynamic-provision/kube-manifest/storageclass.yaml
```

3. Open the **storageclass.yaml** file, paste the code snippet below, and save it.

```bash
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
```

![3.3.1](/images/3-ebs/3.3.1.png)

4. Create a file named **pv-claim.yaml** inside **dynamic-provision/kube-manifest**.

```bash
touch dynamic-provision/kube-manifest/pv-claim.yaml
```

5. Open the **pv-claim.yaml** file, paste the code snippet below, and save it.

```bash
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: ebs-sc
  resources:
    requests:
      storage: 10Gi

```

![3.3.2](/images/3-ebs/3.3.2.png)

6. Create a file named `pod.yaml` inside **dynamic-provision/kube-manifest**.

```bash
touch dynamic-provision/kube-manifest/pod.yaml
```

7. Open the `pod.yaml` file, paste the code snippet below, and save it.

```bash
apiVersion: v1
kind: Pod
metadata:
  name: app
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
      claimName: ebs-claim

```

![3.3.3](/images/3-ebs/3.3.3.png)

#### Deploy Resources and Verify Results

1. Execute the following command.

```bash
kubectl apply -f dynamic-provision/kube-manifest
```

2. Verify the results.

```bash
kubectl get sc,pvc,pod
```

![3.3.4](/images/3-ebs/3.3.4.png)

You should see a Pod named `app` with the status `Running`, a Persistent Volume Claim named `ebs-claim` with the status `Bound` and size `10Gi`, and a Storage Class named `ebs-sc` with the provider `ebs.csi.aws.com`. The Storage Class automatically provisions an EBS Volume with the size defined in `spec.resources.requests.storage` (10Gi).

3. Check the results in the AWS Console. Navigate to **Elastic Block Storage**, and select **Volumes**.

![3.3.5](/images/3-ebs/3.3.5.png)

4. Verify that the Pod writes data to the dynamically provisioned volume successfully.

```bash
kubectl get sc,pvc,pod
```

![3.3.6](/images/3-ebs/3.3.6.png)

You can see data written to the `/data/out.txt` file. Save the first three lines of data for comparison later. Next, delete the current Pod and create a new one to verify the data remains on the EBS volume.

5. Delete the current Pod named `app`.

```bash
kubectl delete pod app
```

6. List all Pods to ensure the Pod is deleted.

```bash
kubectl get pod
```

![3.3.7](/images/3-ebs/3.3.7.png)

7. Create a new Pod.

```bash
kubectl apply -f dynamic-provision/kube-manifest
```

8. List the newly created Pod.

```bash
kubectl get pod
```

![3.3.8](/images/3-ebs/3.3.8.png)

9. Verify the data inside `/data/out.txt` again.

```bash
kubectl exec app -- cat /data/out.txt
```

![3.3.9](/images/3-ebs/3.3.9.png)

The first three lines of data in `/data/out.txt` remain unchanged, even though the Pod was replaced.

#### Cleanup

1. Run the following command to delete all created resources.

```bash
kubectl delete -f dynamic-provision/kube-manifest
```

2. Verify all resources have been successfully deleted.

```bash
kubectl get sc,pvc,pod
```

![3.3.10](/images/3-ebs/3.3.10.png)

Everything has been deleted. Since the Amazon EBS Volume was dynamically created by the Storage Class, it is also removed when the Storage Class is deleted.

3. Go to the EBS Volumes section to check the results.

![3.3.11](/images/3-ebs/3.3.11.png)
