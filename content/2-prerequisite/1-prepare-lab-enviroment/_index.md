+++
title = "Prepare the Environment"
date = 2024
weight = 1
chapter = false
pre = "2.1."
+++

### Configuring EC2

In this step, we will configure an EC2 instance. On the AWS console, locate and select **EC2**.

- Click **Launch Instance**.

![2.1.1](/images/2-prerequisites/2.1.1.png)

- Name: `FCJ-Workspace`.
- Select the operating system: **Ubuntu**.

![2.1.2](/images/2-prerequisites/2.1.2.png)

- Choose Machine Image: **Ubuntu Server 24.04**.
- Select Instance Type: **t3.large**.

![2.1.3](/images/2-prerequisites/2.1.3.png)

- Key pair: **create key pair** if you don’t already have one.

![2.1.4](/images/2-prerequisites/2.1.4.png)

- Security Group: You can select the default security group or create a custom one. Ensure that SSH port 22 is enabled.
- Click **Launch instance** to create the EC2 instance.

![2.1.5](/images/2-prerequisites/2.1.5.png)

#### Connect to EC2 via SSH

- Select **Connect**.

![2.1.7](/images/2-prerequisites/2.1.7.png)

- Choose **SSH Client**.
- Copy the connection string as shown in the image.

![2.1.8](/images/2-prerequisites/2.1.8.png)

- Open Visual Studio Code.
- Choose to connect via SSH and paste the copied connection string.

![2.1.9](/images/2-prerequisites/2.1.9.png)

- Edit the key path in the connection string and click connect.

![2.1.10](/images/2-prerequisites/2.1.10.png)
