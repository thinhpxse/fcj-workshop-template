---
title: "Worklog Tuần 11– Container Services on AWS"
weight: 1
chapter: false
pre: " <b> 1.x. </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây là bản mẫu tham khảo — không được sao chép nguyên văn cho bài nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Hiểu toàn bộ hệ sinh thái dịch vụ Container trên AWS.
* Thực hành triển khai container bằng Lightsail, ECS Fargate, và Amazon EKS.
* Áp dụng Infrastructure as Code cho ECS & EKS với AWS CDK và EKS Blueprints.
* Xây dựng CI/CD pipelines cho ứng dụng containerized.
* Tìm hiểu ROSA – Red Hat OpenShift trên AWS.

---

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                     | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------ |
| 2   | - Tổng quan **Container Services on AWS** <br> - Kiến trúc Lightsail Containers                                         | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - Getting Started with **Amazon Lightsail Containers**                                                                   | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - Amazon ECS Workshop: <br> &emsp; + Containerization on ECS & Fargate <br> &emsp; + ECS Cluster, Task, Service         | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - IaC for ECS with **AWS CDK** <br> - CI/CD Pipeline for ECS Applications                                               | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - Amazon EKS Workshop: Getting Started with EKS <br> - EKS Nodegroups, VPC, RBAC                                        | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 7   | - IaC with **EKS Blueprints for CDK** <br> - CI/CD for EKS Applications                                                  | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 8   | - Deploying First Application to Amazon EKS <br> - Terraform with EKS (Coming Soon)                                      | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 9   | - Red Hat OpenShift Service on AWS (ROSA)                                                                                | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |

---

### Kết quả đạt được:

---

## 🟣 1. Amazon Lightsail Containers

- Hiểu cách Lightsail giúp triển khai container đơn giản và ít cấu hình.  
- Push Docker image → Lightsail Container Service.  
- Tạo scale container, môi trường staging/production nhanh chóng.  
- Sử dụng Load Balancer tích hợp của Lightsail.

---

## 🟢 2. Amazon ECS Workshop (ECS & Fargate)

### Containerization with ECS & Fargate
- Tạo ECS Cluster (Fargate mode không cần quản lý server).  
- Tạo Task Definition: container, CPU, memory, port mapping.  
- Tạo ECS Service + Auto Scaling.  
- Triển khai ứng dụng container hóa toàn diện.

### Infrastructure as Code with AWS CDK
- Dùng CDK để tạo ECS Cluster, Task, Service, Application Load Balancer.  
- IaC giúp tái sử dụng cấu hình và triển khai tự động.

### CI/CD for ECS Applications
- CodePipeline + CodeBuild build image → push ECR → deploy ECS.  
- Tự động hóa full lifecycle CI/CD.

---

## 🔵 3. Amazon EKS Workshop (Kubernetes on AWS)

### Getting Started with EKS
- Tìm hiểu control plane, worker node, node group, RBAC.  
- Deploy EKS cluster và cấu hình kubectl.

### IaC with EKS Blueprints (CDK)
- Tạo EKS cluster bằng CDK Blueprints.  
- Add-ons tự động: VPC CNI, CoreDNS, KubeProxy, ALB Controller…  
- Quản lý cluster theo chuẩn enterprise.

### CI/CD for EKS Applications
- Build Docker image, push ECR → deploy manifest YAML tự động.  
- Blue/Green hoặc Rolling Deployment bằng Kubernetes.

### Deploying First App to EKS
- Tạo deployment + service (ClusterIP/LoadBalancer).  
- Access ứng dụng qua ALB Ingress Controller.

### Terraform with EKS (Coming Soon)
- Ghi chú: chưa triển khai, sẽ học trong phiên bản sắp tới.

---

## 🔴 4. Red Hat OpenShift on AWS (ROSA)

- Hiểu ROSA: dịch vụ OpenShift fully managed trên AWS.  
- Tích hợp Kubernetes enterprise với bảo mật cao.  
- Dùng cho workload yêu cầu compliance hoặc môi trường hybrid.  
- Hỗ trợ deployment tự động, scaling và quản lý bằng OpenShift Console.

---

### Tổng kết tuần:

* Hoàn thiện kiến thức từ container đơn giản (Lightsail) đến orchestration enterprise (EKS).  
* Thành thạo ECS Fargate, CDK IaC, CI/CD cho container workloads.  
* Hiểu kiến trúc Kubernetes và thực hành triển khai trên EKS.  
* Tiếp cận ROSA — giải pháp container enterprise trên AWS.  
* Sẵn sàng thiết kế và vận hành hạ tầng container hiện đại cho dự án thực tế.

