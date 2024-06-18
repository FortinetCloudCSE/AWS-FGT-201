---
title: "Securing Acme Corp's AWS Traffic Flows with FortiGate NGFW"
weight: 1
archetype: home
---

## Acme Corp is migrating on prem apps/workloads to AWS Public cloud

- 3 tier web apps (Front end, middleware, database)

{{% notice tip %}}
**CISO's typically have the following concerns and requirements** when migrating applications & workloads to public cloud. 

In this workshop you will **learn how FortiGates can address the following:**

- Concerns:
  - Exposure to Inbound Internet attacks
  - Environment and App segmentation to reduce exploit blast radius
  - NGFW protection & URL filtering for outbound web traffic 
  - Simple security policy across Corporate Cyber Infrastructure
- Corporate Requirements:
  - Regional HA architecture (multi AZ)
  - Traffic Protection with NGFW featuring FortiGate FortiGuard advanced protection
  - Log of all traffic (blocked and allowed)
{{% /notice %}}

![](1_moduleone/FTNTSecVPC-simple.png)

## Workshop Goals

In this workshop you will learn how to use FortiGate NGFW deployed as AWS EC2 instances to protect traffic flows in common AWS architecture patterns as well as some fundamental AWS networking concepts.

The intent is to help clarify the following:

  * Learn foundational AWS networking concepts such as symmetrical routing traffic in and out of VPCs for various traffic flows
  * Use FortiGate instances in AWS to secure inbound, outbound, and East/West traffic flows
  * Introduction to FortiGate Security VPC (AWS centralized architecture) with Transit Gateway
