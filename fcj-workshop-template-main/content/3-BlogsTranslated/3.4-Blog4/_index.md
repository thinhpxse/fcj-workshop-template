---
title: "Secure Amazon EVS with AWS Network Firewall"
weight: 1
chapter: false
pre: " <b> 3.4. </b> "
---
{{% notice warning %}}
⚠️ **Note:** The information below is for reference only. Please **do not copy verbatim** into official reports, including this notice.
{{% /notice %}}

# Secure Amazon Elastic VMware Service (Amazon EVS) with AWS Network Firewall

Amazon EVS allows organizations to migrate, run, and scale VMware workloads natively on AWS, using VMware Cloud Foundation (VCF) deployed within an Amazon VPC on bare-metal Amazon EC2 instances. This approach enables rapid migration, seamless scaling, and minimal application refactoring.  
([aws.amazon.com](https://aws.amazon.com/blogs/architecture/secure-amazon-elastic-vmware-service-amazon-evs-with-aws-network-firewall/?utm_source=chatgpt.com))

When building a hybrid cloud architecture integrating EVS, AWS VPCs, on-premises data centers, and internet connectivity, it is critical to implement consistent network security. Centralized inspection, unified firewall policy management, and consolidated logging/monitoring are essential. AWS recommends **AWS Network Firewall** as the core inspection and protection layer.  
([aws.amazon.com](https://aws.amazon.com/blogs/architecture/secure-amazon-elastic-vmware-service-amazon-evs-with-aws-network-firewall/?utm_source=chatgpt.com))

This blog explains how to secure an Amazon EVS environment using AWS Network Firewall, applying a centralized inspection model across EVS clusters, VPCs, on-premises environments, and the internet.

---

## Architecture Overview

- AWS Network Firewall acts as a transparent “bump-in-the-wire,” inserted via VPC or Transit Gateway routing, inspecting all traffic without requiring application-level changes.  
- A typical architecture includes:  
  - **EVS VPC**, hosting the underlay VLAN subnets for VCF (management, vMotion, vSAN, NSX overlay, etc.).  
  - **Workload VPCs** (e.g., VPC01) where applications or services run.  
  - **Ingress VPC** with an Application Load Balancer for public traffic.  
  - **Egress VPC** with a NAT Gateway to centralize outbound internet access.  
  - **Optional on-prem connectivity** via AWS Direct Connect + Direct Connect Gateway.  
([aws.amazon.com](https://aws.amazon.com/blogs/architecture/secure-amazon-elastic-vmware-service-amazon-evs-with-aws-network-firewall/?utm_source=chatgpt.com))

All traffic flows—EVS ↔ VPCs, VPC ↔ VPC, VPC ↔ on-prem, and VPC ↔ Internet—are inspected by AWS Network Firewall, covering both **east-west** and **north-south** traffic.

### Key Benefits

- Centralized firewall control across EVS, AWS VPCs, on-premises, and internet paths  
- Unified enforcement of security policies across complex hybrid environments  
- Centralized logging and monitoring for audit, compliance, and troubleshooting  

---

## Implementation Steps (Summary)

1. **Create an AWS Transit Gateway**  
   Disable default association/propagation. Create two route tables:  
   - Pre-inspection  
   - Post-inspection  
   ([aws.amazon.com](https://aws.amazon.com/blogs/architecture/secure-amazon-elastic-vmware-service-amazon-evs-with-aws-network-firewall/?utm_source=chatgpt.com))

2. **Attach EVS, workload VPCs, ingress/egress VPCs, and optional Direct Connect Gateway**  
   Use the Pre-inspection route table for all attachments.

3. **Deploy AWS Network Firewall using Transit Gateway integration**  
   AWS automatically provisions required resources (firewall endpoints, internal routing, etc.).  
   Simplifies configuration and reduces operational overhead.

4. **Update route tables**  
   - Pre-inspection: Default routing directs traffic into the Firewall for inspection  
   - Post-inspection: Routes inspected traffic back to the appropriate VPC destinations  
   - Inside each VPC: Update ingress/egress routes so all flows traverse the firewall  
   ([aws.amazon.com](https://aws.amazon.com/blogs/architecture/secure-amazon-elastic-vmware-service-amazon-evs-with-aws-network-firewall/?utm_source=chatgpt.com))

5. **(Optional) Configure firewall rule groups and logging**  
   - Stateful rules (e.g., HTTP/HTTPS, FQDN filtering, east-west segmentation)  
   - Send flow logs and alert logs to Amazon CloudWatch Logs or Amazon S3  

When configured correctly, all network flows—across EVS, AWS VPCs, on-prem, and the internet—are monitored and protected.

---

## Advantages Over Traditional Security Approaches

- No application-level changes required; firewall operates transparently at the network layer  
- Centralized management simplifies policy enforcement across multiple VPCs and VMware clusters  
- Scales dynamically using AWS-managed infrastructure  
- Unified logs streamline compliance, auditing, and incident analysis  

---

## Conclusion

This blog demonstrates how AWS Network Firewall can secure Amazon EVS by providing a **centralized, scalable network inspection model**. Traffic between EVS, workload VPCs, on-premises environments, and the internet is consistently monitored and enforced.

Organizations can migrate VMware workloads to AWS confidently—preserving the benefits of cloud scalability while maintaining strong network security, unified control, and comprehensive visibility.
