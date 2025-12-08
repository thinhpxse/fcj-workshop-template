---
title: "Week 11"
weight: 11
chapter: false
pre: " <b> 1.11 </b> "
---
{{% notice warning %}}
⚠️ Note: This is a reference template — do not copy verbatim for official submission.
{{% /notice %}}

### Objectives of This Week:

* Understand the entire AWS Container Services ecosystem.
* Practice deploying containers using Amazon Lightsail, ECS Fargate, and Amazon EKS.
* Apply Infrastructure as Code (IaC) for ECS and EKS using AWS CDK and EKS Blueprints.
* Build CI/CD pipelines for containerized applications.
* Learn about ROSA – Red Hat OpenShift Service on AWS.

---

### Tasks to Perform This Week:
| Day | Tasks | Start Date | End Date | Reference |
| --- | ----- | ---------- | -------- | --------- |
| 2 | - Overview of **Container Services on AWS** <br> - Lightsail Containers Architecture | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Getting Started with **Amazon Lightsail Containers** | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - Amazon ECS Workshop: <br> &emsp; + Containerization with ECS & Fargate <br> &emsp; + ECS Cluster, Task, Service | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 5 | - IaC for ECS with **AWS CDK** <br> - CI/CD Pipeline for ECS Applications | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 6 | - Amazon EKS Workshop: Getting Started with EKS <br> - EKS Node Groups, VPC, RBAC | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 7 | - IaC with **EKS Blueprints for CDK** <br> - CI/CD for EKS Applications | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 8 | - Deploying First Application to Amazon EKS <br> - Terraform with EKS (Coming Soon) | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |
| 9 | - Red Hat OpenShift Service on AWS (**ROSA**) | dd/mm/2025 | dd/mm/2025 | https://cloudjourney.awsstudygroup.com/ |

---

### Achievements:

---

## 🟣 1. Amazon Lightsail Containers

- Understood how Lightsail simplifies container deployment with minimal configuration.  
- Pushed Docker images to Lightsail Container Service.  
- Scaled containers and quickly created staging/production environments.  
- Used the built-in Lightsail Load Balancer.

---

## 🟢 2. Amazon ECS Workshop (ECS & Fargate)

### Containerization with ECS & Fargate
- Created ECS Clusters (Fargate mode without managing servers).  
- Built Task Definitions: container image, CPU, memory, and port mappings.  
- Created ECS Services with Auto Scaling.  
- Deployed fully containerized applications.

### Infrastructure as Code with AWS CDK
- Used CDK to create ECS Clusters, Task Definitions, Services, and Application Load Balancers.  
- IaC enabled reusable and automated infrastructure deployment.

### CI/CD for ECS Applications
- Implemented CodePipeline + CodeBuild to build images → push to ECR → deploy to ECS.  
- Fully automated the CI/CD lifecycle.

---

## 🔵 3. Amazon EKS Workshop (Kubernetes on AWS)

### Getting Started with EKS
- Learned about the control plane, worker nodes, node groups, and RBAC.  
- Deployed an EKS cluster and configured `kubectl`.

### IaC with EKS Blueprints (CDK)
- Created EKS clusters using CDK Blueprints.  
- Automatically installed add-ons: VPC CNI, CoreDNS, kube-proxy, ALB Controller, etc.  
- Managed clusters using enterprise-grade best practices.

### CI/CD for EKS Applications
- Built Docker images and pushed to ECR → automatically deployed Kubernetes manifests (YAML).  
- Used Blue/Green and Rolling deployments with Kubernetes.

### Deploying First App to EKS
- Created Kubernetes Deployments and Services (ClusterIP/LoadBalancer).  
- Exposed the application using ALB Ingress Controller.

### Terraform with EKS (Coming Soon)
- Note: Not implemented yet, planned for future learning.

---

## 🔴 4. Red Hat OpenShift on AWS (ROSA)

- Understood ROSA as a fully managed OpenShift service on AWS.  
- Integrated enterprise Kubernetes with strong security and compliance.  
- Suitable for hybrid cloud and high-compliance workloads.  
- Supported automated deployment, scaling, and management via OpenShift Console.

---

### Weekly Summary:

* Completed knowledge from simple container platforms (Lightsail) to enterprise orchestration (EKS).  
* Became proficient in ECS Fargate, CDK-based IaC, and CI/CD for container workloads.  
* Gained hands-on experience with Kubernetes architecture on Amazon EKS.  
* Explored ROSA as an enterprise-grade container solution on AWS.  
* Ready to design and operate modern container infrastructure for real-world projects.
