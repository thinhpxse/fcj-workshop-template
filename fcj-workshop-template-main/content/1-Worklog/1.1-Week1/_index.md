---
title: "Worklog Week 1"
date: 2025-12-09
weight: 1
chapter: false
pre: " <b> 1.1 - 1.2. </b> "
---

{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

### Reference Sources
The summary content is based on the **Explore AWS Services** section of Cloud Journey - AWS Study Group.

---

## Week 1

### Week 1 Objectives
* Build AWS fundamentals: create an account, understand pricing and support mechanisms.  
* Master basic concepts of access management (IAM), networking (VPC), and compute (EC2).  
* Hands-on practice: create an account, configure IAM, deploy EC2, use Cloud9, host a static website on S3, and create a basic RDS database.

### Tasks to be completed this week:

| Day | Task (Details) | Start Date | End Date | Reference Source |
| --- | -------------- | ---------- | -------- | ---------------- |
| 2 | **Creating Your First AWS Account** <br> - Sign up for AWS Free Tier (create root user) <br> - Enable MFA for root, add billing information, and create a billing alarm to avoid unexpected charges. | TBD 08/09/2025 | TBD 08/09/2025 | cloudjourney.awsstudygroup.com |
| 2 | **Managing Costs with AWS Budgets** <br> - Set up Budgets and alerts (email/SNS), use Cost Explorer, and practice creating a budget for a small project. | TBD 09/09/2025 | TBD 09/09/2025 | cloudjourney.awsstudygroup.com |
| 3 | **Getting Help with AWS Support** <br> - Understand support plans, how to create support cases, and use documentation & community (AWS Study Group) for debugging. | TBD 10/09/2025 | TBD 10/09/2025 | cloudjourney.awsstudygroup.com |
| 3 | **Access Management (IAM)** <br> - Create IAM users/groups, basic policies, practice the principle of least privilege, and create roles for services. | TBD 10/09/2025 | TBD 10/09/2025 | cloudjourney.awsstudygroup.com |
| 4 | **Networking Essentials (VPC)** <br> - Create a VPC, public/private subnets, route tables, Internet Gateway, NAT Gateway; understand CIDR, security groups vs NACLs. | TBD 10/09/2025 | TBD 10/09/2025 | cloudjourney.awsstudygroup.com |
| 4 | **Compute Essentials (EC2)** <br> - Launch an EC2 instance (AMI, instance type), configure SSH key pairs and security groups; manage EBS volumes and basic snapshots. | TBD 11/09/2025 | TBD 11/09/2025 | cloudjourney.awsstudygroup.com |
| 5 | **Instance Profiling with IAM Roles for EC2** <br> - Create IAM roles for EC2 and attach policies so the instance can access S3/RDS/CloudWatch without access keys. | TBD 11/09/2025 | TBD 11/09/2025 | cloudjourney.awsstudygroup.com |
| 5 | **Cloud Development with AWS Cloud9** <br> - Open the Cloud9 IDE, get familiar with the workspace, and deploy a small app (Node/Python) from Cloud9 to EC2 or S3. | TBD 11/09/2025 | TBD 11/09/2025 | cloudjourney.awsstudygroup.com |
| 6 | **Static Website Hosting with Amazon S3** <br> - Create a bucket, configure static website hosting, set up public policies (or use CloudFront for security), and deploy a simple HTML/CSS site. | TBD 12/09/2025 | TBD 12/09/2025 | cloudjourney.awsstudygroup.com |
| 6 | **Database Essentials (Amazon RDS)** <br> - Create an RDS instance (MySQL/PostgreSQL), configure subnet groups, security groups, backup windows, and test connections from EC2. | TBD 12/09/2025 | TBD 12/09/2025 | cloudjourney.awsstudygroup.com |
| 7 | **Simplified Computing (Amazon Lightsail)** <br> - Explore how to use Lightsail to quickly deploy VMs or apps and compare costs and convenience with EC2. | TBD 12/09/2025 | TBD 12/09/2025 | cloudjourney.awsstudygroup.com |
| 7 | **Container Deployment with Amazon Lightsail Containers** <br> - Try deploying a simple container on Lightsail Containers to understand the workflow: image push, deployment, and public endpoint. | TBD 12/09/2025 | TBD 12/09/2025 | cloudjourney.awsstudygroup.com |

### Week 1 Results
* Created and secured the AWS account (MFA, billing alarms) and understood basic pricing and support.  
* Understood IAM fundamentals and created test users/roles/policies.  
* Set up a simple VPC, deployed EC2, connected via SSH, and tested EBS and snapshots.  
* Used Cloud9 for rapid development and hosted a static website on S3.  
* Created a basic RDS instance and tested connectivity; deployed a simple app on Lightsail and Lightsail Containers.
