+++  
title = "Amazon EKS Persistent Storage with Amazon EFS"  
date = 2024  
weight = 4 
chapter = false  
pre = "4."  
+++

### Overview

![architecture](/images/4-efs/architecture.png)

Amazon Elastic File System (EFS) is a simple, serverless, elastic file system that allows you to set it up and forget it while using it with AWS Cloud services and on-premises resources. It is designed to scale on demand up to petabytes without disrupting applications, automatically growing and shrinking as you add and remove files, eliminating the need to provision and manage capacity to accommodate growth.

### Contents

1. [Install EFS CSI Driver](1-install-efs-csi)
2. [Create EFS File System](2-create-efs-file-system)
3. [Static Provisioning](3-static-provisioning)
4. [Dynamic Provisioning](4-dynamic-provisioning)
