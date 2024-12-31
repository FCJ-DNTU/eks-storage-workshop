+++
title = "Create IAM Role"
date = 2024
weight = 2
chapter = false
pre = "2.2."
+++

### Creating an IAM Role

In this section, we will create an administrator role for EC2.

- On the AWS console, locate and select **IAM**.
- Click **Role**.
- Select **Create Role**.

![2.2.1](/images/2-prerequisites/2.2.1.png)

- Choose **AWS Service**.
- Select **EC2**.

![2.2.2](/images/2-prerequisites/2.2.2.png)

- Select **AdministratorAccess**.

![2.2.3](/images/2-prerequisites/2.2.3.png)

- Role name: `eksworkspace-administrator`.
- Description: `Allow EC2 instance to call AWS Services on your behalf`.

![2.2.4](/images/2-prerequisites/2.2.4.png)

- Click **Create role**.

![2.2.5](/images/2-prerequisites/2.2.5.png)
