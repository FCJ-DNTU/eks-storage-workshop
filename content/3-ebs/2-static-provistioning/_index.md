+++
title = "Static Provisioning"
date = 2024
weight = 2
chapter = false
pre = "3.2."
+++

In this step, we will practice how to create and consume a PersistentVolume from a pre-created EBS volume with static provisioning.

#### Check the Node's Availability Zone

First, we need to check which Availability Zone the EC2 Managed Node Group Instance is running in.

- Go to the EC2 Instances.
- Check its Availability Zone

![3.2.1](/images/3-ebs/3.2.1.png)

In this case the Node's Availability Zone is ap-southeast-1a, so the EBS Volume will be created at ap-southeast-1a.

#### Create PersistentVolume from EBS Volume

- Go to EBS Volumes.
- You will see the default volume of NodeGroup.
- Click Create volume to create a new volume.

![3.2.2](/images/3-ebs/3.2.2.png)

- Replace 20 in Size (GiB).
- Set Availability Zone to the Availability Zone of Node. In this case, it is ap-southeast-1a.

![3.2.3](/images/3-ebs/3.2.3.png)

- Select **Create Volume**

![3.2.4](/images/3-ebs/3.2.4.png)

Edit the Name of the newly created EBS volume `fcj-ebs-strorage-eks`.

![3.2.5](/images/3-ebs/3.2.5.png)

Save the Volume ID for later use.

![3.2.6](/images/3-ebs/3.2.6.png)

#### Create manifest files

- In the VS Code command window, create a folder named `static-provision/kube-manifest`.

```bash
mkdir -p static-provision/kube-manifest
ls
ls static-provision
```

![3.2.7](/images/3-ebs/3.2.7.png)

- Create a file named pv.yaml inside static-provision/kube-manifest.

```bash
touch static-provision/kube-manifest/pv.yaml
```

![3.2.8](/images/3-ebs/3.2.8.png)

- Create a file named pv-claim.yaml inside static-provision/kube-manifest.

```bash
touch static-provision/kube-manifest/pv-claim.yaml
```

- Open the pv-claim.yaml file, paste the code below. Then save.

```bash
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
name: ebs-claim
spec:
storageClassName: "" # Empty string must be explicitly set otherwise default StorageClass will be set
volumeName: test-pv
accessModes:
- ReadWriteOnce
resources:
requests:
storage: 10Gi
```

![3.2.9](/images/3-ebs/3.2.9.png)

- Create a file named pod.yaml inside static-provision/kube-manifest.
- Open the pod.yaml file, paste the code below. Then save.

```bash
touch static-provision/kube-manifest/pod.yaml
```

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

![3.2.10](/images/3-ebs/3.2.10.png)

#### Deploy resources

- Execute the command below.
- Apply configuration

```bash
kubectl apply -f static-provision/kube-manifest
```

![3.2.11](/images/3-ebs/3.2.11.png)

#### Check the result

- List Persistent Volume (PV).
- There is a PV named test-pv with a capacity of 10GB.

```bash
kubectl get pv
```

- List Persistent Volume Claim (PVC).
- There is a PVC named ebs-claim, status is BOUND, disk is test-pv and capacity is 10GB.

```bash
kubectl get pvc
```

- List Pod
- There is a Pod named app with a status of Running.

```bash
kubectl get pod
```

![3.2.12](/images/3-ebs/3.2.12.png)

- We will verify that the Pod writes data to the statically provisioned volume successfully.

There are many data written to the /data/out.txt file. Save the contents of the first three data for later comparison.

```bash
kubectl exec app -- cat /data/out.txt
```

![3.2.13](/images/3-ebs/3.2.13.png)

Now, we will delete the Pod then recreate another one to check the data in the EBS volume that has been fixed to the new Pod.

- Delete the current Pod
- List all Pods again to make sure the Pod is deleted

```bash
kubectl delete pod app
kubectl get pod
```

![3.2.14](/images/3-ebs/3.2.14.png)

- Create a new Pod.
- List the newly created Pod.

```bash
kubectl apply -f static-provision/kube-manifest
kubectl get pod
```

![3.2.15](/images/3-ebs/3.2.15.png)

- Verify the data in /data/out.txt again.

```bash
kubectl exec app -- cat /data/out.txt
```

![3.2.16](/images/3-ebs/3.2.16.png)

As you can see, the first three data in /data/out.txt are still Mon May 6 17:15:04 UTC 2024, Mon May 6 17:15:09 UTC 2024, and Mon May 6 17:15:14 UTC 2024. The data is kept while the Pod is replaced with another one.

#### Cleanup

- Execute the command below to delete all resources.
- Verify that all resources have been deleted successfully.

```bash
kubectl delete -f static-provision/kube-manifest
kubectl get pv,pvc,pod
```

![3.2.17](/images/3-ebs/3.2.17.png)

All deleted.

- Go to EBS Volume to delete the volume named fcj-ebs-strorage-eks.
- Select the volume named fcj-ebs-strorage-eks.
- Click Actions.
- Click Delete volume

![3.2.18](/images/3-ebs/3.2.18.png)

- Type delete to confirm.
- Then, click Delete.
