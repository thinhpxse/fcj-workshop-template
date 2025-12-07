---
title : "Workshop Overview"
date : 2025-12-6
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

#### Understanding VPC Endpoints

**VPC Endpoints** enable private connectivity between your Virtual Private Cloud (VPC) and AWS services without requiring traffic to traverse the public internet. These endpoints are:
- **Highly Available**: Built with redundancy across multiple Availability Zones
- **Scalable**: Automatically scale to handle your workload demands
- **Secure**: Keep traffic within the AWS network, reducing exposure to internet-based threats

There are two primary types of VPC endpoints:
1. **Gateway Endpoints**: Used for connecting to Amazon S3 and DynamoDB. These are specified as route table targets for traffic destined to supported AWS services.
2. **Interface Endpoints (AWS PrivateLink)**: Enable private connectivity to services powered by AWS PrivateLink, including many AWS services, your own services, and SaaS applications. These create elastic network interfaces (ENIs) in your subnets.

#### Lab Architecture Overview

This hands-on workshop demonstrates secure access to Amazon S3 from multiple network environments using VPC endpoints. The lab architecture consists of two distinct Virtual Private Clouds:

**Cloud VPC Environment:**
- Hosts cloud-native AWS resources including EC2 instances for testing
- Contains a **Gateway VPC Endpoint** providing direct, private access to Amazon S3
- Represents your production AWS cloud infrastructure
- Demonstrates how cloud workloads can securely access S3 without internet exposure

**On-Premises Simulation VPC:**
- Emulates a traditional on-premises data center or branch office environment
- Includes an EC2 instance configured with VPN software (OpenSwan/strongSwan) to establish secure connectivity
- Connected to AWS through a **Site-to-Site VPN** via AWS Transit Gateway
- Features an **Interface VPC Endpoint** enabling on-premises resources to access S3 privately over the VPN connection
- Simulates hybrid cloud scenarios where on-premises applications need secure S3 access

**Network Connectivity:**
The two VPCs are interconnected through **AWS Transit Gateway**, which acts as a cloud router to enable secure communication between your cloud and simulated on-premises environments. The Site-to-Site VPN connection ensures encrypted traffic flow between the environments.

**Important Notes:**
- This lab uses a single VPN tunnel for cost efficiency and simplicity
- For production deployments, AWS strongly recommends implementing redundant VPN connections across multiple devices and Availability Zones for high availability
- The architecture follows AWS Well-Architected Framework principles for security and reliability

![Workshop Architecture Diagram](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

This workshop will guide you through configuring both Gateway and Interface VPC Endpoints, testing connectivity from both environments, and understanding best practices for securing S3 access in hybrid cloud architectures.