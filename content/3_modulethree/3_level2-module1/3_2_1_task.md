---
title: "Transit Gateway w/ BGP"
weight: 3
---


## **Dynamic Routing** 
|                            |    |  
|----------------------------| ----
| **Goal**                   | Utilize dynamic routing with Transit Gateway and FortiGates.
| **Task**                   | Create attachment associations & propagations, update/create FortiGate routes and firewall policy to allow secured traffic.
| **Verify task completion** | Confirm outbound and east/west connectivity from EC2 Instance-A via Ping, HTTP, HTTPS.

![](../image-tgw-dynamic-example.png)

#### Summarized Steps (click to expand each for details)

0. Lab Environment Setup

    {{% expand title="**Detailed Steps...**" %}}

- **0.1:** In the **QwikLabs Console left menu** find and copy the URL from the output **TemplateC**.
- **0.2:** In your AWS account, navigate to the **CloudFormation Console**, click **Create stack** in the upper right, then **With new resources (standard)**.
- **0.3:** **Paste** the URL copied previously into the **Amazon S3 URL** and click **Next**.
- **0.4:** Provide an alphanumeric name for the stack, such as part3, task3, etc and cick **Next**.
- **0.5:** **You must select an IAM role in the Permissions section** of the configure stack options page, then scroll down and click **Next**.
  ![](image-t0-1.png)
  {{% notice warning %}}
**If you do not select a the IAM role and continue with stack creation, this will fail!** If this occurred, simply create another stack with a different name and follow the steps closely for this section. 
  {{% /notice %}}

- **0.6:** On the review and create page, scroll to the bottom, check the boxes to acknowledge the warnings, and click **Submit**.
  ![](image-t0-2.png)
- **0.7:** Once the main/root CloudFormation stack shows as **Create_Complete**, proceed with the steps below.

    {{% /expand %}}

1. Check the Transit Gateway Route Tables and confirm east/west is not working.

    {{% expand title = "**Detailed Steps...**" %}}

- **1.1:** In the **VPC Console** go to the **Tranist gateway route tables page** (menu on the left) and check out all of the **associations, propagations, and routes** for **each Transit gateway route table**.  You should see the following:

TGW-RTB Name | Associations
---|---
TGW-Connect-security-tgw-rtb | TGW-Connect-security-vpc-attachment & TGW-Connect-security-connect-attachment & unnamed-vpn-attachment |
TGW-Connect-spoke-tgw-rtb | VPC-A-spoke-vpc-attachment & VPC-B-spoke-vpc-attachment |
TGW-Connect-sharedservices-tgw-rtb | VPC-C-spoke-vpc-attachment |

TGW-RTB Name | Propagations
---|---
TGW-Connect-security-tgw-rtb | VPC-A-spoke-vpc-attachment & VPC-B-spoke-vpc-attachment |
TGW-Connect-spoke-tgw-rtb | VPC-C-spoke-vpc-attachment & TGW-Connect-security-vpc-attachment & TGW-Connect-security-connect-attachment & unnamed-vpn-attachment |
TGW-Connect-sharedservices-tgw-rtb | VPC-A-spoke-vpc-attachment & VPC-B-spoke-vpc-attachment |

TGW-RTB Name | Routes
---|---
TGW-Connect-security-tgw-rtb | 10.1.0.0/16 & 10.2.0.0/16 & 10.3.0.0/16 |
TGW-Connect-spoke-tgw-rtb | 10.0.0.0/16 & 10.3.0.0/16 |
TGW-Connect-sharedservices-tgw-rtb | 10.1.0.0/16 & 10.2.0.0/16 |

- **1.2:** Navigate to the **EC2 Console** and go to the **Instances page** (menu on the left).
- **1.3:** Find the **Instance-A** instance and select it.
- **1.4:** click **Connect > EC2 serial console**.
    - **Copy the instance ID** as this will be the username and click connect.
- **1.5:** Login to the EC2 instance:
    - username: <<copied Instance ID from above>>
    - Password: **`FORTInet123!`** 
- **1.6:** Run the command **`ping 10.2.2.10`** to ping Instance-B.
- **1.7:** Run the command **`curl 10.2.2.10`** to connect to Instance-B.
   - These **SHOULD NOT** work at this point.

    {{% /expand %}}
		
2. Review FortiGate1's GRE + BGP config and advertise a summary route to Transit Gateway.

    {{% expand title = "**Detailed Steps...**" %}}

- **2.1:** Navigate to the **CloudFormation Console** and **toggle View Nested to off**.
- **2.2:** Select the main template and select the **Outputs tab**.
- **2.3:** Login to **FortiGate1**, using the outputs **FGT1LoginURL**, **Username**, and **Password**.
- **2.4:** Upon login in the **upper right hand corner** click on the **>_** icon to open a CLI session.
- **2.5:** Run the command **`show system gre`** and notice **the interface and IPs that are used are private**.
- **2.6:** Run the command **`show system interface tgw-conn-peer`** and notice **the IPs assigned to the inside interfaces of the GRE tunnel**.
- **2.7:** Run the command **`show router bgp`** and notice the **BGP peers fall into the remote-ip CIDR** in the output of the command above.
- **2.8:** Run the command **`get router info routing-table all`** and notice the **routes received match the routes tab of TGW-Connect-security-tgw-rtb**.
- **2.8:** Copy and paste the commands below to configure a 10.0.0.0/8 summary route to be advertised back to Transit gateway:
  ```
  config router bgp
  config aggregate-address
  edit 1
  set prefix 10.0.0.0 255.0.0.0
  set summary-only enable
  next
  end
  end
  ```
- **2.9:** Run the commands below to confirm that the 10.0.0.0/8 route is now being advertised to our BGP neighbors.
  ```
  get router info bgp summary
  get router info bgp neighbors 169.254.6.2 advertised-routes
  get router info bgp neighbors 169.254.6.2 advertised-routes
  ```
    {{% /expand %}}

3. Test secured east/west connectivity from Instance-A to Instance-B.

    {{% expand title = "**Detailed Steps...**" %}}

- **3.1:** While still in the console session for Instance-A
- **3.2:** Run the command **`ping 10.2.2.10`** to ping Instance-B.
- **3.3:** Run the command **`curl 10.2.2.10`** to connect to Instance-B.
   - Ping and curl should connect successfully through FGT1 which is connected to Transit Gateway over a Connect attachment (GRE + BGP over a VPC attachment).
- **3.4:** Run the command **`ping 8.8.8.8`** to ping a resource on the internet.
- **3.5:** Run the command **`curl ipinfo.io`** to connect to a resource on the internet.
  - These **SHOULD NOT** work at this point.

    {{% /expand %}}

4. Let's dig deeper to understand how all of this works.....

    {{% expand title = "**Detailed Steps...**" %}}

- **4.1:** High level diagram showing how the Connect attachment goes over a VPC attachment which allows FortiGate1 to have a GRE tunnel over a private path which has BGP peering configured as well.  This provides an overlay tunnel where dynamic rotues and data-plane traffic can be routed without adding additional routes to the VPC router.
	
![](../image-tgw-dynamic-example-flow1.png)
	
{{% notice info %}}	
FortiGate1 is getting data-plane traffic over a GRE tunnel between it's port2 private IP (10.0.3.x/24) and an IP out of Transit Gateway CIDR block (100.64.0.x/24).  This GRE tunnel is going over the TGW-Connect-security-connect-attachment, so this is all over a private path. Also Transit Gateway can support jumbo frames up to 8500 bytes for traffic between VPCs, AWS Direct Connect, Transit Gateway Connect, and peering attachments. However, traffic over VPN connections can have an MTU of 1500 bytes. Find out more in [**AWS Documentation**](https://docs.aws.amazon.com/vpc/latest/tgw/transit-gateway-quotas.html#mtu-quotas).

BGP peering can be either iBGP or eBGP but the IP addressing will always us the inside tunnel IPs from a specific selection of CIDRs from 169.254.0.0/16.  To find out which ones can or can't be used, please reference [**AWS Documentation**](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-connect.html#tgw-connect-peer).

Regardless which type of BGP is used, each connect peer is only required to create one GRE tunnel to peer to the redundant BGP peers on the Transit Gateway side. For more information, reference [**AWS Documentation**](https://aws.amazon.com/blogs/networking-and-content-delivery/simplify-sd-wan-connectivity-with-aws-transit-gateway-connect/).
{{% /notice %}}

    {{% /expand %}}

5. Review FortiGate2's VPN, BGP, route-map, and configure default-route-originate with a route-map.

    {{% expand title = "**Detailed Steps...**" %}}

- **5.1:** Navigate to the **CloudFormation Console** and **toggle View Nested to off**.
- **5.2:** Select the main template and select the **Outputs tab**.
- **5.3:** Login to **FortiGate2**, using the outputs **FGT2LoginURL**, **Username**, and **Password**.
- **5.4:** Upon login in the **upper right hand corner** click on the **>_** icon to open a CLI session.
- **5.5:** Run the command **`show vpn ipsec phase1-interface`** and notice **there are two tunnels where the remote-gw values are public IPs and the interfaces are port1**.
- **5.6:** Run the command **`show router route-map rmap-aspath1`** and notice **the as-path is set to 6500**.
- **5.7:** Copy and paste the commands below to configure default-route-originate with the route-map to advertise 0.0.0.0/0 with an as-path of 6500:
  ```
  config router bgp
  config neighbor
  edit 169.254.10.1
  set capability-default-originate enable
  set default-originate-routemap rmap-aspath1
  next
  edit 169.254.11.1
  set capability-default-originate enable
  set default-originate-routemap rmap-aspath1
  next
  end
  end
  ```
- **5.8:** Run the commands below to confirm that the 0.0.0.0/0 route is now being advertised to our BGP neighbors with an as-path of 6500.
  ```
  get router info bgp summary
  get router info bgp neighbors 169.254.10.1 advertised-routes
  get router info bgp neighbors 169.254.11.1 advertised-routes
  ```

    {{% /expand %}}

6. Test secured egress connectivity from Instance-A through FortiGate2.

    {{% expand title = "**Detailed Steps...**" %}}

- **6.1:** While still in the console session for Instance-A
- **6.2:** Run the command **`ping 8.8.8.8`** to ping a resource on the internet.
- **6.3:** Run the command **`curl ipinfo.io`** to connect to a resource on the internet.
   - Ping and curl should connect successfully through FGT2 which is connected to Transit Gateway over a VPC attachment (IPsec + BGP over the internet).
   - The public IP returned from ipinfo.io should match the public IP of FGT2
- **6.4:** In the **VPC Console** go to the **Transit gateway route tables page**.
- **6.5:** Find **TGW-Connect-spoke-tgw-rtb** go to the **Routes tab** and notice there are **ECMP routes for 0.0.0.0/0** since there are two VPN tunnels configured.

    {{% /expand %}}

7. Let's dig deeper to understand how all of this works.....

    {{% expand title = "**Detailed Steps...**" %}}
	
{{% notice info %}}	
FortiGate2 is getting data-plane traffic over **two IPsec tunnels** between its port1 private ip (10.0.2.x/24) that has an associated [**Elastic IP**](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) and two public IPs managed by AWS, so this is all over a public path but encrypted. This means that jumbo frames are not supported for VPN based attachments.

Since there are two tunnels with a BGP peer configured for each, FortiGate2 is able to advertise ECMP paths which Transit Gateway can use.  [**TGW supports ECMP**](https://docs.aws.amazon.com/vpc/latest/tgw/how-transit-gateways-work.html#tgw-ecmp) for different attachment types.
{{% /notice %}}

    {{% /expand %}}


8. Configure FortiGate1 with default-route-orignate with a route-map as well.

    {{% expand title = "**Detailed Steps...**" %}}

- **8.1:** Login to **FortiGate1**, using the outputs **FGT1LoginURL**, **Username**, and **Password**.
- **8.2:** Upon login in the **upper-right hand corner** click on the **>_** icon to open a CLI session.
- **8.3:** Run the command **`show router route-map rmap-aspath1`** and notice **the as-path is set to 6500 just like FortiGate2**.
- **8.4:** Copy and paste the commands below to configure default-route-originate with the route-map to advertise 0.0.0.0/0 with an as-path of 6500:
  ```
  config router bgp
  config neighbor
  edit 169.254.6.2
  set capability-default-originate enable
  set default-originate-routemap rmap-aspath1
  next
  edit 169.254.6.3
  set capability-default-originate enable
  set default-originate-routemap rmap-aspath1
  next
  end
  end
  ```
- **8.5:** Run the commands below to confirm that the 0.0.0.0/0 route is now being advertised to our BGP neighbors with an as-path of 6500.
  ```
  get router info bgp summary
  get router info bgp neighbors 169.254.6.2 advertised-routes
  get router info bgp neighbors 169.254.6.3 advertised-routes
  ```
    {{% /expand %}}

9. Test secured egress connectivity from Instance-A through FortiGate1.

    {{% expand title = "**Detailed Steps...**" %}}

- **9.1:** While still in the console session for Instance-A
- **9.2:** Run the command **`ping 8.8.8.8`** to ping a resource on the internet.
- **9.3:** Run the command **`curl ipinfo.io`** to connect to a resource on the internet.
   - Ping and curl should connect successfully through FGT1 which is connected to Transit Gateway over a Connect attachment (GRE + BGP over a VPC attachment).
   - The public IP returned from ipinfo.io should match the public IP of FGT1
- **9.4:** In the **VPC Console** go to the **Transit gateway route tables page**.
- **9.5:** Find **TGW-Connect-spoke-tgw-rtb** go to the **Routes tab** and notice there **is only one route for 0.0.0.0/0 since Transit Gateway only supports ECMP routing from the same attachment types**.

    {{% /expand %}}


10. Let's dig deeper to understand how all of this works.....

    {{% expand title = "**Detailed Steps...**" %}}
	
{{% notice info %}}
While Transit Gateway does support ECMP routing, it only does so for the same attachment types. There is a [**route evaluation order**](https://docs.aws.amazon.com/vpc/latest/tgw/how-transit-gateways-work.html#tgw-route-evaluation-overview) that takes place. The reason behind this is that different attachment types go over different paths (ie connect over private vpc-attachment vs vpc over public internet) which can have different latency, RTTs, etc and even different MTUs supported.
{{% /notice %}}

    {{% /expand %}}

11. Lab Environment Teardown

    {{% expand title="**Detailed Steps...**" %}}

- **11.1:** Navigate to the **CloudFormation Console**, select the main stack you created and click **Delete**.
- **11.2:** Once the stack is deleted, proceed to the next task.

    {{% /expand %}}

### Discussion Points
- TGW supports ECMP routing with routes from the same attachment type.
   - This allows scalable active-active centralized ingress/egress inspection.
   - East/West inspection requires SNAT to keep flows sticky to the same FortiGate'
- TGW has a route evaluation priority to select the best path for the same route.
- Each TGW VPC connection (2x IPsec tunnels per connection) supports up to 1.5 Gbps.
- Each TGW Connect peer supports up to 5 Gbps.
- TGW supports multiple peers per TGW Connect attachment and multiple attachments to a single VPC.

**This concludes this task**
