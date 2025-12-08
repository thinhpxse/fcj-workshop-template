---
title: "Worklog Week 10 – Serverless Web App Workshop & Elastic Beanstalk & WordPress on AWS"
weight: 1
chapter: false
pre: " <b> 1.x. </b> "
---
{{% notice warning %}}
⚠️ Note: This is a reference template only — do not copy verbatim for official submission.
{{% /notice %}}

### Objectives of This Week:

* Understand how to build serverless web applications and integrate APIs.
* Deploy Node.js applications using Elastic Beanstalk and automate CI/CD with AWS CDK.
* Learn WordPress architecture on AWS and run WordPress on Amazon EC2.
* Compare Serverless Web Apps, Elastic Beanstalk, and EC2-hosted WordPress.

---

### Tasks to Perform This Week:
| Day | Tasks | Start Date | End Date | Reference |
| --- | ----- | ---------- | -------- | --------- |
| 2 | - Overview of **Serverless Web App Workshop** <br> - Build Serverless APIs (Lambda + API Gateway) | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Build **Serverless Chat Application** <br> - WebSocket API + DynamoDB Streams | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - **Elastic Beanstalk Workshop** <br> - Deploy Node.js application on Elastic Beanstalk | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 5 | - CI/CD with Elastic Beanstalk + **CDK Pipelines** <br> - Automated deployment from source → production | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 6 | - **WordPress Architecture on AWS** <br> - WordPress Well-Architected design | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 7 | - Run WordPress on EC2: <br> &emsp; + EC2 <br> &emsp; + RDS <br> &emsp; + EFS/FSx <br> &emsp; + ALB | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |

---

### Achievements:

## 🟣 1. Serverless Web App Workshop

### 🔧 Building Serverless APIs
- Built APIs using **Amazon API Gateway** and **AWS Lambda**.  
- Implemented routing, stages, integrations, and security (CORS, Cognito authentication).  
- Stored data in DynamoDB and designed tables based on access patterns.

### 💬 Serverless Chat Application
- Used **API Gateway WebSocket APIs** to build a real-time chat system.  
- Used DynamoDB Streams to push real-time messages to connected clients.  
- Lambda functions handled events in a fully event-driven architecture.  
- The application automatically scaled without server management.

---

## 🟢 2. Elastic Beanstalk Workshop

### 🚀 Deploying Node.js Applications
- Uploaded source code to Elastic Beanstalk.
- Elastic Beanstalk automatically provisioned EC2, Auto Scaling Groups, Load Balancers, and Security Groups.
- Learned deployment policies: Rolling, Rolling with Additional Batch, Immutable, etc.

### 🔄 CI/CD with Elastic Beanstalk & CDK Pipelines
- Built automated pipelines for build and deployment.  
- Used **AWS CDK** to define Elastic Beanstalk environments and pipeline infrastructure.  
- Reduced deployment errors and delivery time.

---

## 🔵 3. WordPress on AWS

### 🏗 WordPress Architecture on AWS
- Learned the standard AWS architecture:
  - EC2 for the PHP application servers  
  - Amazon RDS (MySQL) for the database  
  - EFS/FSx for shared `wp-content` storage  
  - ALB for load balancing  
  - CloudFront + S3 for optimized media delivery
- Understood how to scale WordPress across multiple instances.

### 💻 Running WordPress on Amazon EC2
- Launched EC2 and installed LAMP/LEMP stack.  
- Connected WordPress to Amazon RDS MySQL.  
- Mounted shared storage using EFS/FSx.  
- Configured basic security and backups.  
- Tuned performance and caching.

---

### Weekly Summary:

* Completed hands-on experience with serverless APIs, real-time WebSocket chat, DynamoDB, and event-driven architectures.  
* Became proficient in deploying Node.js applications with Elastic Beanstalk and CI/CD using AWS CDK.  
* Gained solid understanding of optimized WordPress architecture on AWS and practical deployment on EC2.  
* Ready to combine Serverless, Elastic Beanstalk, and EC2 models for real-world projects.
