---
title: "Securing Acme Corp's AWS Network with FortiGate NGFW"
weight: 1
archetype: home
---

Acme Corp is migrating an on-premises 3-tier web app (presentation, application, database) to Amazon Web Services (AWS).

You will learn how FortiGates can address the following standard **concerns and requirements** when migrating applications and workloads to public cloud. 

**Concerns:**    
  - Exposure to inbound internet attacks
  - Environment and application segmentation to reduce exploit blast radius
  - Next generation firewall (**NGFW**) protection and URL filtering for outbound web traffic 
  - Simple security policy across corporate cyber infrastructure    
  
**Corporate Requirements:**    
  - Regional, highly availabile architecture (multiple availability zones (AZs))
  - NGFW protection featuring FortiGate FortiGuard advanced protection
  - Logging of all traffic

![](1_moduleone/FTNTSecVPC-simple.png)

## Workshop Goals

You will learn how to use FortiGate NGFW deployed as AWS EC2 instances to protect traffic flows in common AWS architecture patterns, as well as some fundamental AWS networking concepts.

The intent is to help clarify the following:    
  * Foundational AWS networking concepts such as symmetrical routing traffic in and out of VPCs for various traffic flows
  * Use of FortiGate instances in AWS to secure inbound, outbound, and East/West traffic flows
  * AWS centralized architecture with Transit Gateway (TGW), and the FortiGate security VPC concept
