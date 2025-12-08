---
title: "Week 7"
weight: 1
chapter: false
pre: " <b> 1.7 </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây chỉ là bản mẫu tham khảo, không sao chép nguyên văn cho bài nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Hiểu các kỹ thuật tối ưu hiệu năng hệ thống khi vận hành trên AWS.
* Làm quen với Docker, ECS, CI/CD và workflow orchestration.
* Thực hành mô hình DevOps để tự động hóa build, deploy và scaling.
* Nắm được hiệu năng lưu trữ, hybrid storage và khả năng xử lý NoSQL DynamoDB.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                               | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------ |
| 2   | - Tổng quan **AWS Performance Optimization** <br> - Vai trò của containerization & orchestration                                                        | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - Học **Containerization với Docker** <br> - Build Dockerfile & best practices                                                                          | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - **Amazon ECS**: cluster, task, service <br> - So sánh EC2 mode và Fargate mode                                                                        | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - **Infrastructure as Code cho ECS** với AWS CDK <br> - Tự động sinh ECS Service/Task/Load Balancer bằng CDK                                           | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - CI/CD với **AWS CodePipeline** <br> - Tự động build → test → deploy <br> - DevOps với CodePipeline                                                   | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 7   | - Hybrid Storage với **AWS Storage Gateway** <br> - Windows File Storage với **Amazon FSx**                                                             | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 8   | - **DynamoDB Advanced Applications** <br> - Workflow orchestration với **AWS Step Functions** <br> - Storage Performance Workshop                        | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |


### Kết quả đạt được:

#### 🐳 Containerization & Orchestration
- Hiểu rõ Docker: build image, layers optimization, push/pull registry.  
- Nắm vững Amazon ECS:  
  - Task Definition  
  - Service Auto Scaling  
  - ECS Cluster (Fargate & EC2 mode)  
- Hiểu cách triển khai container workload hiệu suất cao trên AWS.

#### 🧱 Infrastructure as Code for ECS
- Sử dụng **AWS CDK** để khai báo ECS Service, Load Balancer, Task Definition.  
- Quản lý cấu hình ECS dễ dàng hơn với IaC, đảm bảo reproducibility.

#### 🔄 CI/CD & DevOps Automation
- Tự động hóa triển khai bằng **AWS CodePipeline** và CodeBuild.  
- Pipeline tự động chạy:  
  - Source → Build → Test → Deploy  
- Áp dụng DevOps cho ECS workload.

#### 💾 Storage Performance
- **AWS Storage Gateway**: tăng tốc hybrid storage và caching.  
- **Amazon FSx**: hiệu năng cao cho ứng dụng Windows (SMB).  
- Tham gia Storage Performance Workshop để nắm về throughput, IOPS, scaling.

#### ⚡ High Performance NoSQL
- **Amazon DynamoDB**:  
  - Key-value & document store hiệu năng cao  
  - Adaptive capacity  
  - On-demand vs provisioned mode  
  - Global Tables cho performance toàn cầu

#### 🔁 Workflow Orchestration
- Sử dụng **AWS Step Functions** để xây dựng workflow có trạng thái.  
- Điều phối nhiều dịch vụ AWS trong một pipeline hiệu suất cao.

---

### Tổng kết tuần:

* Nắm vững kỹ thuật tối ưu hiệu năng từ container, CI/CD, cho tới storage và NoSQL.  
* Biết cách áp dụng DevOps với Docker + ECS + CDK + CodePipeline.  
* Hiểu được chiến lược tăng performance và auto-scaling trong hệ thống phân tán.  
* Sẵn sàng triển khai workload Containerized & High-Performance trên AWS Production.

