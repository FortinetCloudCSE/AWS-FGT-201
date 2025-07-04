---
title: "Workshop Hands-On"
weight: 3
---


![](image-getready.jpg)

## Tasks in this workshop

In this workshop, we will explore AWS common networking architectures and FortiGate Deployments to secure traffic flows in them. These architectures will be used to demonstrate how to secure traffic flows for the following services and network scenarios:
  1. [**VPC Peering**](3_modulethree/31_task.html)
  2. [**Transit Gateway with VPC attachments**](3_modulethree/32_task.html)
  3. [**Transit Gateway with Connect and VPN attachments**](3_modulethree/33_task.html)
  4. [**Gateway Load Balancer w/ distributed and centralized networking**](3_modulethree/34_task.html)
  5. [**Cloud WAN with VPC and Connect attachments**](3_modulethree/35_task.html)

{{% notice note %}}
#### EC2 Instance Connect
In each of these scenarios, you'll need to connect to EC2 instances via Instance Connect.  

From the AWS console, follow these directions to connect to the specific instance for the given task instruction:
  - In the **EC2 Console** go to the **Instances page** select the **TASK_SPECIFIC_INSTANCE**.
  - Click **Connect > EC2 serial console**.
      - **Copy the instance ID** as this will be the username and **click Connect**. 
  - Login to the EC2 instance:
    - You may need to hit **ENTER** to get a login prompt
        - username: <<copied Instance ID from above>>
        - Password: **`FORTInet123!`** 

{{% /notice %}}