---
title: "Week 6"
weight: 1
chapter: false
pre: " <b> 1.6 </b> "
---

⚠️ Note: This is only a sample template. Do not submit it as your official assignment.

### Weekly Objectives

- Understand the Reliability pillar of the AWS Well-Architected Framework.
- Learn how to design resilient systems: backup, failover, high availability, and network reliability.
- Practice implementing reliability solutions: Backup, Multi-Attach, SQS/SNS, Clustering.
- Recognize High Availability models for Databases and Windows Server on AWS.

---

### Tasks for This Week

| Day | Tasks | Start Date | End Date | References |
| --- | ----- | ---------- | -------- | ---------- |
| 2 | - Overview of **AWS Reliability** <br> - Principles: Fault Tolerance, High Availability, Backup, Failover | dd/mm/2025 | dd/mm/2025 | cloudjourney.awsstudygroup.com |
| 3 | - **AWS Backup**: configure backup plan, vault, and backup rules <br> - Learn data protection strategies | dd/mm/2025 | dd/mm/2025 | cloudjourney.awsstudygroup.com |
| 4 | - Network Reliability: **VPC Peering** <br> - Centralized network management with **Transit Gateway** | dd/mm/2025 | dd/mm/2025 | cloudjourney.awsstudygroup.com |
| 5 | - Messaging Systems: **SQS** (queue), **SNS** (pub-sub) <br> - Applying messaging for distributed architectures | dd/mm/2025 | dd/mm/2025 | cloudjourney.awsstudygroup.com |
| 6 | - Shared storage using **Amazon EBS Multi-Attach** <br> - Database HA with Multi-Attach + Systems Manager | dd/mm/2025 | dd/mm/2025 | cloudjourney.awsstudygroup.com |
| 7 | - **Windows Server Failover Clustering (WSFC)** on AWS <br> - Advanced failover architectures | dd/mm/2025 | dd/mm/2025 | cloudjourney.awsstudygroup.com |
| 8 | - SQL Server High Availability on AWS: <br> &emsp; + Version 2019 <br> &emsp; + Version 2022 | dd/mm/2025 | dd/mm/2025 | cloudjourney.awsstudygroup.com |

---

### Achievements

#### 🛡 Data Protection
- Learned how to use **AWS Backup** for centralized backup management.
- Configured backup plans, vaults, lifecycle policies, and cross-region backup.
- Compared AWS Backup with manual snapshots.

#### 🌐 Network Reliability
- **VPC Peering**: low-latency, private connection between VPCs without traversing the Internet.
- **AWS Transit Gateway**: manage hundreds of VPCs and on-prem networks using a hub-and-spoke model.
- Gained knowledge on designing highly available network architectures.

#### 📩 Messaging for Reliability
- **Amazon SQS**: asynchronous processing, load decoupling, preventing message loss.
- **Amazon SNS**: publish-subscribe model, fan-out architecture for microservices.
- Applied messaging patterns to build resilient and decoupled systems.

#### 💾 Storage Reliability
- **EBS Multi-Attach**: share a single EBS volume across multiple EC2 instances → improved HA for shared-disk applications.
- Used Systems Manager automation to configure database HA.

#### 🪟 Windows & SQL Server High Availability
- **Windows Server Failover Clustering (WSFC)** on AWS: shared storage, automated failover nodes.
- **SQL Server HA (2019 & 2022)** on AWS:
  - Always On Availability Groups  
  - Multi-AZ deployment  
  - Automatic failover  

---

### Weekly Summary

- Gained a complete understanding of how to improve AWS infrastructure reliability across storage, networking, and messaging layers.
- Learned to implement High Availability for Windows Server and SQL Server workloads.
- Built strong foundational knowledge for designing AWS architectures aligned with the Reliability pillar of the Well-Architected Framework.
- Ready to apply HA/DR models in real-world Production environments.
