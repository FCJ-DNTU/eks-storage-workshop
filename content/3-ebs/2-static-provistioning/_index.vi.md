+++  
title = "Cung cấp tĩnh"  
date = 2024  
weight = 2
chapter = false  
pre = "3.2."  
+++

Trong bước này, chúng ta sẽ thực hành làm thế nào để tạo và tiêu thụ một PersistentVolume từ một ổ đĩa EBS được tạo sẵn với cung cấp tĩnh.

#### Kiểm tra Availability Zone của Node

Đầu tiên, chúng ta cần kiểm tra Availability Zone nào mà EC2 Managed Node Group Instance đang hoạt động.

- Đi đến EC2 Instances.
- Kiểm tra Availability Zone của nó

![3.2.1](/images/3-ebs/3.2.1.png)

Ở trường hợp này Availability Zone của Node là ap-southeast-1a, vì thế EBS Volume sẽ được tạo tại ap-southeast-1a.

#### Tạo PersistentVolume từ EBS Volume

- Đi đến EBS Volumes.
- Bạn sẽ thấy ổ đĩa mặc định của NodeGroup.
- Nhấn Create volume để tạo ổ đĩa mới.

![3.2.2](/images/3-ebs/3.2.2.png)

- Thay thế thành 20 tại Size (GiB).
- Đặt Availability Zone là Availability Zone của Node. Trong trường hợp này là ap-southeast-1a.

![3.2.3](/images/3-ebs/3.2.3.png)

- Chọn **Create Volume**

![3.2.4](/images/3-ebs/3.2.4.png)

Chỉnh sửa Name của ổ đĩa EBS vừa tạo `fcj-ebs-strorage-eks`.

![3.2.5](/images/3-ebs/3.2.5.png)

Lưu lại Volume ID để sử dụng sau.

![3.2.6](/images/3-ebs/3.2.6.png)

#### Tạo các tệp manifest

- Tại cửa sổ lệnh VS Code, tạo một thư mục tên `static-provision/kube-manifest`.

```bash
mkdir -p static-provision/kube-manifest
ls
ls static-provision
```

![3.2.7](/images/3-ebs/3.2.7.png)

- Tạo một tệp tên pv.yaml bên trong static-provision/kube-manifest.

```bash
touch static-provision/kube-manifest/pv.yaml
```

![3.2.8](/images/3-ebs/3.2.8.png)

- Tạo một tệp tên pv-claim.yaml bên trong static-provision/kube-manifest.

```bash
touch static-provision/kube-manifest/pv-claim.yaml
```

- Mở tệp pv-claim.yaml, dán đoạn mã code dưới đây. Sau đó lưu lại.

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

- Tạo một tệp tên pod.yaml bên trong static-provision/kube-manifest.
- Mở tệp pod.yaml, dán đoạn mã code bên dưới. Sau đó lưu lại.

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

#### Triển khai tài nguyên

- Thực thi câu lệnh dưới đây.
- Apply cấu hình

```bash
kubectl apply -f static-provision/kube-manifest
```

![3.2.11](/images/3-ebs/3.2.11.png)

#### Kiểm tra kết quả

- Liệt kê Persistent Volume (PV).
- Có một PV tên test-pv với dung lượng là 10GB.

```bash
kubectl get pv
```

- Liệt kê Persistent Volume Claim (PVC).
- Có một PVC tên ebs-claim, trạng thái là BOUND, ổ đĩa là test-pv và dung lượng là 10GB.

```bash
kubectl get pvc
```

- Liệt kê Pod
- Có một Pod tên app với trạng thái là Running.

```bash
kubectl get pod
```

![3.2.12](/images/3-ebs/3.2.12.png)

- Chúng ta sẽ xác minh Pod viết dữ liệu vào ổ đĩa được cung cấp tĩnh thành công.
- Có nhiều dữ liệu được viết vào tệp /data/out.txt. Hãy lưu lại nội dung của ba dữ liệu đầu tiên để so sánh sau.

```bash
kubectl exec app -- cat /data/out.txt
```

![3.2.13](/images/3-ebs/3.2.13.png)

Bây giờ, chúng ta sẽ xóa Pod sau đó tạo lại một cái khác để kiểm tra dữ liệu trong ổ đĩa EBS đã được cố định với Pod mới.

- Xoá Pod hiện tại
- Liệt kê tất cả các Pod lần nữa để đảm bảo Pod đã được xóa

```bash
kubectl delete pod app
kubectl get pod
```

![3.2.14](/images/3-ebs/3.2.14.png)

- Tạo một Pod mới.
- Liệt kê Pod vừa tạo.

```bash
kubectl apply -f static-provision/kube-manifest
kubectl get pod
```

![3.2.15](/images/3-ebs/3.2.15.png)

- Xác minh dữ liệu trong /data/out.txt lần nữa.

```bash
kubectl exec app -- cat /data/out.txt
```

![3.2.16](/images/3-ebs/3.2.16.png)

Bạn có thể thấy, ba dữ liệu đầu tiên trong /data/out.txt hiện vẫn là Mon May 6 17:15:04 UTC 2024, Mon May 6 17:15:09 UTC 2024 và Mon May 6 17:15:14 UTC 2024. Dữ liệu được giữ nguyên trong khi Pod được thay thế bằng cái khác.

#### Dọn dẹp

- Thực thi câu lệnh dưới để xóa tất cả tài nguyến.
- Xác minh rằng tất cả các tài nguyên đã được xóa thành công.

```bash
kubectl delete -f static-provision/kube-manifest
kubectl get pv,pvc,pod
```

![3.2.17](/images/3-ebs/3.2.17.png)

Tất cả đã được xóa.

- Đi đến EBS Volume để xóa ổ đĩa tên fcj-ebs-strorage-eks.
- Chọn ổ đĩa tên fcj-ebs-strorage-eks.
- Nhấn Actions.
- Nhấn Delete volume

![3.2.18](/images/3-ebs/3.2.18.png)

- Nhập delete để xác nhận.
- Sau đó, nhấn Delete.
