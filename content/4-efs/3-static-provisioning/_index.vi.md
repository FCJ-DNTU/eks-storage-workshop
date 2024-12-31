---
title: "Cung cấp tĩnh"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 4.3 </b> "
---

### Lưu trữ các tài nguyên hiện tại.

1. Tạo một thư mục tên **EBS** để lưu trữ các tài nguyên của phần **EBS**.

```
mkdir EBS
```

2. Di chuyển tất cả các thư mục **dynamic-provision** và **static-provision** đến thư mục **EBS**.

```
mv dynamic-provision EBS
mv static-provision EBS
```

![static-provisition](/images/4-efs/4.3.1.png)

### Tạo các tệp Manifest.

1. Tạo thư mục tên `EFS` để lưu trữ các tệp kube-manifest.

```
mkdir EFS
```

2. Tạo thư mục cho phần **Static Provisioning**.

```
mkdir -p EFS/static-provision/kube-manifest
ls EFS/static-provision
```

![static-provisition](/images/4-efs/4.3.2.png)

3. Tạo tệp tên **storageclass.yaml** bên trong **static-provision/kube-manifest**.

```
touch EFS/static-provision/kube-manifest/storageclass.yaml
```

4. Mở tệp **storageclass.yaml**, dán đoạn mã code bên dưới.

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: efs-sc
provisioner: efs.csi.aws.com
```

![static-provisition](/images/4-efs/4.3.3.png)

5. Tạo một tệp tên **pv.yaml** bên trong **static-provision/kube-manifest**.

```
touch EFS/static-provision/kube-manifest/pv.yaml
```

6. Mở tệp **pv.yaml**, dán đoạn mã code bên dưới. Nhập EFS File System ID của bạn tại **volumeHandle**.

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

![static-provisition](/images/4-efs/4.3.4.png)

7. Tạo tệp tên **pvc.yaml** bên trong **static-provision/kube-manifest**.

```
touch EFS/static-provision/kube-manifest/pvc.yaml
```

8. Mở tệp **pvc.yaml**, dán đoạn mã code bên dưới.

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

![static-provisition](/images/4-efs/4.3.5.png)

9. Tạo một tệp tên **pod.yaml** bên trong **static-provision/kube-manifest**.

```
touch EFS/static-provision/kube-manifest/pod.yaml
```

10. Mở tệp **pod.yaml**, dán đoạn mã code bên dưới.

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

### Triển khai tài nguyên

1. Thực thi câu lệnh bên dưới để triển khai tài nguyên.

```
kubectl apply -f EFS/static-provision/kube-manifest
```

2. Liệt kê tất cả tài nguyên đã được tạo.

```
kubectl get sc,pv,pvc,pod
```

![static-provisition](/images/4-efs/4.3.7.png)

{{% notice important %}}
**STATUS** của **Persistent Volume (pv)** và **Persistent Volume Claim (pvc)** phải là **Bound**, và của **Pod** phải là **Running**.
{{% /notice %}}

### Kiểm tra kết quả.

1. Thực thi câu lệnh dưới để xác minh rằng dữ liệu đã được ghi vào EFS filesystem.

```
kubectl exec -ti efs-app -- head /data/out.txt
```

![static-provisition](/images/4-efs/4.3.8.png)

Có nhiều dữ liệu được viết vào tệp **/data/out.txt**. Hãy lưu lại nội dung của ba dữ liệu đầu tiên để so sánh sau.

2. Bây giờ chúng ta sẽ xóa Pod hiện tại. Sau đó liệt kê tất cả các tài nguyên lần nữa để đảm bảo Pod đã được xóa.

```
kubectl delete pod efs-app
kubectl get sc,pv,pvc,pod
```

![static-provisition](/images/4-efs/4.3.9.png)

Pod **efs-app** đã được xóa.

3. Tạo một Pod mới.

```
kubectl apply -f EFS/static-provision/kube-manifest/pod.yaml
kubectl get pod
```

4. Xác minh dữ liệu bên trong **/data/out.txt** lần nữa.

```
kubectl exec -ti efs-app -- head /data/out.txt
```

![static-provisition](/images/4-efs/4.3.10.png)

Bạn có thể thấy, ba dữ liệu đầu tiên trong **/data/out.txt** hiện vẫn là **Wed May 8 11:31:17 UTC 2024**, **Wed May 8 11:31:22 UTC 2024** và **Wed May 8 11:31:27 UTC 2024**. Dữ liệu được giữ nguyên trong khi Pod được thay thế bằng cái khác.

### Dọn dẹp

1. Thực thi câu lệnh bên dưới để xóa tất cả tài nguyên.

```
kubectl delete -f EFS/static-provision/kube-manifest
```

2. Xác minh tất cả tài nguyên đã được xóa thành công.

```
kubectl get sc,pv,pvc,pod
```

![static-provisition](/images/4-efs/4.3.11.png)

Tất cả đã được xóa.
