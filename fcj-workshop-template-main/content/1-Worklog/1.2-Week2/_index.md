---
title: "Week 2"

weight: 1
chapter: false
pre: " <b> 1.2 </b> "
---
{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy it verbatim** for your report, including this warning.
{{% /notice %}}

### Week 2 Objectives
* Improve operational capabilities: autoscaling, monitoring, DNS, CDN, CLI, and NoSQL / caching databases.  
* Practice deploying systems with basic scaling & high availability, using CloudWatch for monitoring and Route 53 for DNS configuration.  
* Get familiar with DynamoDB, ElastiCache, CloudFront, Lambda@Edge, and basic Windows/Directory administration on AWS.

### Tasks to be carried out this week:
| Day | Task (details) | Start Date | Completion Date | Reference Material |
| --- | -------------- | ----------- | ---------------- | ------------------ |
| 2   | **Scaling Applications with EC2 Auto Scaling** <br> - Create Launch Template/Configuration, Auto Scaling Group (ASG), scale-out/scale-in policies; test by simulating increased load. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 2   | **Monitoring with Amazon CloudWatch** <br> - Create CloudWatch Alarm, custom metrics, log group; configure dashboard to monitor CPU, network, health check. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 3   | **Hybrid DNS Management with Amazon Route 53** <br> - Create hosted zone, record sets (A/CNAME/ALIAS), health check; combine internal DNS with public DNS if needed. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 3   | **Command Line Operations with AWS CLI** <br> - Install & configure AWS CLI, practice EC2/S3/RDS/DynamoDB commands, small scripts to automate frequent tasks. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 4   | **NoSQL Database Essentials (Amazon DynamoDB)** <br> - Create table, design partition key & sort key, read/write items, test throughput (read/write capacity / on-demand). | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 4   | **In-Memory Caching with Amazon ElastiCache** <br> - Deploy Redis/Memcached; integrate caching for applications; configure cluster replication (if needed) and test reduced latency. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 5   | **Networking on AWS Workshop** <br> - Advanced exercises on VPC, peering, overview of VPN/Direct Connect, review flow logs and network troubleshooting. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 5   | **Content Delivery with Amazon CloudFront** <br> - Create distribution, origin (S3/ALB), invalidation, verify TTL; use CloudFront to accelerate static assets. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 6   | **Edge Computing with CloudFront and Lambda@Edge** <br> - Write small Lambda@Edge functions to modify headers/redirect; understand edge compute use cases. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 6   | **Windows Workloads on AWS** <br> - Deploy EC2 Windows instance, license model, RDP access, basic patching & backup configuration. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 7   | **Directory Services (AWS Managed Microsoft AD)** <br> - Initialize AWS Managed Microsoft AD, domain join EC2 Windows, test authentication and simple group policy. | TBD | TBD | cloudjourney.awsstudygroup.com. |
| 7   | **Building Highly Available Web Applications** <br> - Design multi-AZ for EC2/RDS, load balancing (ALB), health checks, automatic scaling with ASG, use CloudFront + Route 53 to enhance HA and performance. | TBD | TBD | cloudjourney.awsstudygroup.com. |

### Week 2 Outcomes:
* Understand and configure Auto Scaling for EC2 applications; able to simulate scale-out/scale-in.  
* Set up CloudWatch metrics/alarms and dashboards for basic monitoring; know how to collect logs.  
* Configure DNS with Route 53, use CloudFront to optimize content delivery; experiment with simple Lambda@Edge functions.  
* Deploy DynamoDB and ElastiCache; understand trade-offs between RDBMS / NoSQL / cache.  
* Perform basic Windows workload administration and test AWS Managed Microsoft AD for authentication.

---
