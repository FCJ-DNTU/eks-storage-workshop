+++
title = "Persistent Storage on Amazon EKS"
date = 2024
weight = 1
chapter = false
+++

# Persistent Storage on Amazon EKS

![architecture](/images/1-introduction/1.1.png)

#### Overview

In this workshop, you will learn how to integrate two AWS storage services, **Amazon EBS** and **Amazon EFS**, as persistent storage solutions for your **Amazon EKS** cluster. These services provide the foundation for scalable, reliable, and durable storage solutions for containerized applications.

#### Amazon EBS

**Amazon Elastic Block Store (EBS)** is a high-performance block storage service optimized for workloads requiring high throughput and transactional intensity. This service allows attaching storage volumes to EC2 instances and Kubernetes pods, providing persistent storage for data-intensive applications.

#### Amazon EFS

**Amazon Elastic File System (EFS)** is a fully managed, scalable, and elastic file system. It is ideal for workloads such as big data analytics, web serving, content management, development and testing, and media workflows. EFS automatically replicates data across multiple **Availability Zones** to ensure high availability.

{{% notice note %}}
To configure optimal storage for your EKS cluster, it is essential to understand Kubernetes concepts such as Persistent Volumes (PV), Persistent Volume Claims (PVC), and Storage Classes. This workshop will guide you step-by-step through these concepts.
{{% /notice %}}

#### Kubernetes Storage Concepts

- **Persistent Volumes (PV):** A storage resource in Kubernetes created manually or dynamically. PVs have a lifecycle independent of any pod.
- **Persistent Volume Claims (PVC):** A user request for storage that binds to a PV. PVC specifies storage capacity and access modes (e.g., ReadWriteOnce, ReadOnlyMany, ReadWriteMany).
- **Storage Classes:** Define different types of storage, often linked to quality of service levels or backup policies.
- **Dynamic Volume Provisioning:** Automatically provisions storage upon user request, eliminating the need for manual setup by administrators.

#### Workshop Focus

This workshop focuses on integrating **Amazon EBS** and **Amazon EFS** with your EKS cluster through:

- **Static Provisioning:** Manual storage configuration.
- **Dynamic Provisioning:** Automatically provisioned storage based on user requests.

#### Workshop Content

1. [Introduction to Amazon EBS and EFS](1-introduction/)
2. [Prerequisites](2-prerequisites/)
3. [EKS with Amazon EBS](3-ebs/)
4. [EKS with Amazon EFS](4-efs/)
5. [Resource Cleanup](5-cleanup/)
