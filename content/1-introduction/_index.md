+++
title = "1.Introduction"
date = 2024
weight = 1
chapter = false
+++

In this section, we will introduce the concept of persistent storage on Amazon Elastic Kubernetes Service (EKS). Persistent storage is crucial for Kubernetes applications that need to store data beyond the lifecycle of individual containers or pods. We'll focus on two AWS services that integrate seamlessly with EKS to provide scalable and reliable storage solutions: **Amazon Elastic Block Store (EBS)** and **Amazon Elastic File System (EFS)**.

#### Amazon EBS (Elastic Block Store)

Amazon EBS is a high-performance, block-level storage service designed for workloads requiring high throughput and low latency. It is ideal for applications that require persistent data storage, including databases and high-performance applications. EBS volumes can be attached to EC2 instances and Kubernetes pods, providing a consistent and scalable storage solution.

- **Supported by:** EC2 Instances
- **Use Cases:** High-performance databases, transactional applications, and persistent storage for Kubernetes applications.

#### Amazon EFS (Elastic File System)

Amazon EFS is a fully managed, scalable file storage service that supports both EC2 instances and Fargate tasks. It is ideal for use cases such as big data analytics, content management, web serving, and backup solutions. EFS provides low-latency, shared access to file data, making it perfect for applications that require persistent file storage across multiple Kubernetes pods in different Availability Zones.

- **Supported by:** EC2 Instances and Fargate
- **Use Cases:** File storage for applications that require low-latency access, including content management systems, data analytics, and web servers.

---

#### Kubernetes Storage Concepts

To successfully integrate persistent storage into EKS, it is important to understand key Kubernetes storage concepts:

- **Persistent Volumes (PV):** A storage resource in Kubernetes that exists independently of the pod lifecycle. PVs are either statically or dynamically provisioned by the administrator and represent the underlying storage infrastructure.
- **Persistent Volume Claims (PVC):** A request for storage by a user or application, tied to a specific PV. A PVC specifies the size and access modes required (e.g., ReadWriteOnce, ReadOnlyMany).
- **Storage Classes:** Defines different types of storage, often linked with different levels of service or backup policies. Kubernetes does not dictate what the storage class represents, but it allows for customization based on application needs.
- **Dynamic Provisioning:** A feature that allows storage to be provisioned automatically when requested, eliminating the need for manual intervention by the administrator.

In this workshop, we will cover how to configure **Amazon EBS** and **Amazon EFS** for persistent storage in EKS using both static and dynamic provisioning techniques.
