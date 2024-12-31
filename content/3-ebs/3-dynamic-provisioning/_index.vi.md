+++  
title = "Cung cấp động"  
date = 2024  
weight = 3
chapter = false  
pre = "3.3."  
+++

#### Tạo các tệp manifest

1. tải cửa sổ lệnh VS Code, tạo một tử mục tên `dynamic-provision/kube-manifest`

```bash
mkdir -p dynamic-provision/kube-manifest
```

2. Tạo một tệp tên `storageclass.yaml` bên trong **dynamic-provision/kube-manifest**.

```bash
touch dynamic-provision/kube-manifest/storageclass.yaml
```

3. Mở tệp **storageclass.yaml**, dán đoạn mã code bên dưới. Sau đó lưu lại.

```bash
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
```

![3.3.1](/images/3-ebs/3.3.1.png)

4. Tạo một tệp tên **pv-claim.yaml** bên trong **dynamic-provision/kube-manifest**.

```bash
touch dynamic-provision/kube-manifest/pv-claim.yaml
```

5. Mở tệp **pv-claim.yaml**, dán đoạn mã code bên dưới. Sau đó lưu lại.

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

![3.3.1](/images/3-ebs/3.3.2.png)

6. Tạo một tệp tên pod.yaml bên trong dynamic-provision/kube-manifest.

```bash
touch dynamic-provision/kube-manifest/pod.yaml
```

7. Mở tệp pod.yaml, dán đoạn mã code bên dưới. Sau đó lưu lại.

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

![3.3.1](/images/3-ebs/3.3.3.png)

#### Triển khai tài nguyên, kiểm tra kết quả

1. Thực thi câu lệnh dưới.

```bash
kubectl apply -f dynamic-provision/kube-manifest
```

2. Kiểm tra kết quả

```bash
kubectl get sc,pvc,pod
```

![3.3.1](/images/3-ebs/3.3.4.png)

Có một Pod tên app với trạng thái là Running, một Persistent Volume Claim tên ebs-claim với trạng thái là Bound là dung lượng là 10Gi và một Storage Class tên ebs-sc với Provider là ebs.csi.aws.com. Storage Class sẽ tự động tạo một EBS Volume với kích cỡ ổ đĩa được định nghĩa tại spec.resources.requests.storage (10Gi)..

3. Kiểm tra kết quả ở console. Vào **Elastic Block Storage**, chọn **Volumes**

![3.3.1](/images/3-ebs/3.3.5.png)

4. Chúng ta sẽ xác minh Pod viết dữ liệu vào ổ đĩa được cung cấp động thành công.

```bash
kubectl get sc,pvc,pod
```

![3.3.1](/images/3-ebs/3.3.6.png)

Có nhiều dữ liệu được viết vào tệp /data/out.txt. Hãy lưu lại nội dung của ba dữ liệu đầu tiên để so sánh sau. Bây giờ, chúng ta sẽ xóa Pod sau đó tạo lại một cái khác để kiểm tra dữ liệu trong ổ đĩa EBS đã được cố định với Pod mới.

5. Xóa Pod hiện tại app.

```bash
kubectl delete pod app
```

6. Liệt kê tất cả các Pod để đảm bảo rằng chúng đã được xóa.

```bash
kubectl get pod
```

![3.3.1](/images/3-ebs/3.3.7.png)

7. Tạo một Pod mới.

```bash
kubectl apply -f dynamic-provision/kube-manifest
```

8. Liệt kê Pod đã tạo.

```bash
kubectl get pod
```

![3.3.1](/images/3-ebs/3.3.8.png)

9. Xác minh dữ liệu bên trong /data/out.txt lần nữa.

```bash
kubectl exec app -- cat /data/out.txt
```

![3.3.1](/images/3-ebs/3.3.9.png)

Bạn có thể thấy, ba dữ liệu đầu tiên trong /data/out.txt . Dữ liệu được giữ nguyên trong khi Pod được thay thế bằng cái khác.

#### Dọn Dẹp

1. Thực thi câu lệnh bên dưới để xóa tất cả tài nguyên đã tạo.

```bash
kubectl delete -f dynamic-provision/kube-manifest
```

2. Kiểm tra tất cả tài nguyên đã được xóa thành công.

```bash
kubectl get sc,pvc,pod
```

![3.3.1](/images/3-ebs/3.3.10.png)

Tất cả đã được xóa. Bởi vì Amazon EBS Volume được tạo tĩnh bởi Storage Class. Nên khi Storage Class bị xóa, Amazon EBS Volume cũng sẽ bị loại bỏ.

3. Đi đến EBS Volume để kiểm tra kết quả

![3.3.1](/images/3-ebs/3.3.11.png)
