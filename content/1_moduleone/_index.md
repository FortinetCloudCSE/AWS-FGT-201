---
title: "Introduction"
weight: 1
---

# FortiGate: Protecting AWS Traffic Flows in Advanced Architectures

## Learning Objectives

Upon completion of this workshop, you will gain an understanding of:
  
  * AWS Advanced networking architectures
  * FortiGate deployments to secure traffic in each of these architectures
  * AWS VPC Peering
  * AWS Transit Gateway (TGW)
  * AWS Gateway Load Balancer (GWLB)
  * Creating and applying firewall policies with security profiles and objects to control traffic flows *(10 minutes)*
  * Testing traffic flows to validate the implemented networking and security controls *(20 minutes)*

## Workshop Components

The following Fortinet & AWS components will be used during this workshop:

  * AWS EC2 instances (Amazon Linux OS, FortiGate NGFW)
  * AWS networking components:
    * VPCs
    * Subnets
    * VPC Peering
    * Route Tables (RTBs)
    * Transit Gateway (TGW)
    * Gateway Load Balancer (GWLB)
  * FortiGate instances running FortiOS (Amazon Machine Images (AMI) on EC2)

## AWS Reference Architecture Diagram

AWS networking offers multiple ways to organize your AWS architecture to take advantage of FortiGate traffic inspection. The most important part of designing your network is to ensure **traffic follows a symmetrical routing path** (for forward and reverse flows). As long as flows are symmetrical, the architecture will work and traffic will flow through the FortiGate NGFW for inspection.
  
We will investigate the configuration of different architecture patterns, including:  

  * **VPC Peering**  
  * **FortiGate Centralized Architecture for Selective inspection of East/West & Egress traffic**
  * **FortiGate Centralized Architecture with Dynamic Routing for inspection of East/West & Egresss Traffic with multiple VPCs**
  * **Fortigate Centralized Inspection architecture featuring AWS GWLB**
