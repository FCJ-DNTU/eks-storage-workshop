+++
title = "Cài đặt thư viện"
date = 2024
weight = 3
chapter = false
pre = "2.3."
+++

#### Cài đặt thư viện

Ở phần này chúng ta sẽ tiến hành cài đặt một số thư viện

- Ở terminal copy lệnh sau để tải **AWS CLI**

```bash
sudo snap install aws-cli --classic
aws --version
```

![2.3.1](/images/2-prerequisites/2.3.1.png)

- Update môi trường

```bash
sudo apt update
sudo apt install snapd
```

![2.3.2](/images/2-prerequisites/2.3.2.png)

- Cài đặt kubectl

```bash
sudo snap install kubectl --classic
sudo kubectl versionS
sudo kubectl version --client
```

![2.3.3](/images/2-prerequisites/2.3.3.png)

- Cài đặt eksctl

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin
eksctl version

```

![2.3.4](/images/2-prerequisites/2.3.4.png)
