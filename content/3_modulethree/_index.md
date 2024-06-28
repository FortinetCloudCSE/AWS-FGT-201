---
title: "Workshop Hands-On Agenda"
weight: 3
---


![](image-getready.jpg)

## Tasks in this workshop
- AWS common networking architectures and FortiGate Deployments to secure traffic flows in them:
  1. [**VPC Peering**](3_modulethree/3_1_1_task.html)
  2. [**Tranist Gateway with VPC attachments**](3_modulethree/3_1_2_task.html)
  3. [**Transit Gateway with Connect and VPN attachmentss**](3_modulethree/3_2_1_task.html)
  4. [**Gateway Load Balancer w/ distributed and centralized networking**](3_modulethree/3_2_2_task.html)

#### EC2 Instance Connect
- In each of these scenarios, you'll need to connect to EC2 instances via Instance Connect.  
- Follow these directions to connect to the specific instance for the given task verification steps
  - In the **EC2 Console** go to the **Instances page** select the **TASK_SPECIFIC_INSTANCE**.
  - Click **Connect > EC2 serial console**.
      - **Copy the instance ID** as this will be the username and click connect. 
  - Login to the EC2 instance:
    - You may need to hit **ENTER** to get a login prompt
        - username: <<copied Instance ID from above>>
        - Password: **`FORTInet123!`** 
