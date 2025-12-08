---
title: "Week 10"
weight: 10
chapter: false
pre: " <b> 1.10 </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây chỉ là bản mẫu tham khảo — không sao chép nguyên văn cho bài nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Hiểu cách xây dựng ứng dụng web serverless và tích hợp API.
* Triển khai ứng dụng Node.js với Elastic Beanstalk và tự động hóa CI/CD bằng CDK.
* Nắm kiến trúc WordPress trên AWS và chạy WordPress trên EC2.
* So sánh giữa Serverless Web Apps, Elastic Beanstalk và EC2-hosted WordPress.

---

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                 | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------ |
| 2   | - Tổng quan **Serverless Web App Workshop** <br> - Xây dựng Serverless APIs (Lambda + API Gateway)                                                        | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - Xây dựng **Serverless Chat Application** <br> - WebSocket API + DynamoDB Streams                                                                        | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - Workshop **Elastic Beanstalk** <br> - Deploy ứng dụng Node.js trên Elastic Beanstalk                                                                    | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - CI/CD với Elastic Beanstalk + **CDK Pipelines** <br> - Triển khai tự động hóa từ source → deploy                                                        | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - Kiến trúc **WordPress trên AWS** <br> - WordPress Well-Architected Architecture                                                                          | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 7   | - Chạy WordPress trên EC2: <br> &emsp; + EC2 <br> &emsp; + RDS <br> &emsp; + EFS/FSx <br> &emsp; + ALB                                                     | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |

---

### Kết quả đạt được:

---

## 🟣 1. Serverless Web App Workshop

### 🔧 Building Serverless APIs
- Xây dựng API bằng **Amazon API Gateway** + **AWS Lambda**.  
- Routing, stages, integrations và security (CORS, Cognito auth).  
- Lưu trữ dữ liệu bằng DynamoDB, thiết kế bảng tối ưu theo access patterns.

### 💬 Serverless Chat Application
- Sử dụng **API Gateway WebSocket API** để tạo real-time chat.  
- DynamoDB Streams để đẩy message real-time đến tất cả client.  
- Lambda xử lý từng event → kiến trúc event-driven hoàn chỉnh.  
- Ứng dụng không cần server, tự scale theo số kết nối.

---

## 🟢 2. Elastic Beanstalk Workshop

### 🚀 Deploying Node.js Applications
- Upload source lên Elastic Beanstalk.  
- EB tự động tạo: EC2, ASG, Load Balancer, Security Groups.  
- Nắm Deployment Policies: Rolling, Rolling with Additional Batch, Immutable…

### 🔄 CI/CD with Elastic Beanstalk & CDK Pipelines
- Tạo pipeline tự động build + deploy.  
- Dùng **AWS CDK** để khai báo môi trường Elastic Beanstalk + Pipeline.  
- Giảm sai xót và thời gian triển khai.

---

## 🔵 3. WordPress on AWS

### 🏗 WordPress Architecture on AWS
- Kiến trúc tiêu chuẩn gồm:  
  - EC2 cho PHP server  
  - Amazon RDS MySQL để quản lý database  
  - EFS/FSx để chia sẻ thư mục wp-content  
  - ALB để cân bằng tải  
  - CloudFront + S3 để tối ưu media  
- Hiểu cách scale nhiều instance WordPress.

### 💻 Running WordPress on Amazon EC2
- Tạo EC2 + cài đặt LAMP/LEMP stack.  
- Kết nối WordPress đến RDS MySQL.  
- Mount shared storage EFS/FSx.  
- Cấu hình bảo mật và backup cơ bản.  
- Tối ưu performance & caching.

---

### Tổng kết tuần:

* Hoàn thiện kiến thức serverless: API, real-time WebSocket chat, DynamoDB, event-driven.  
* Thành thạo deployment Node.js trên Elastic Beanstalk cùng pipeline CDK.  
* Nắm kiến trúc WordPress tối ưu trên AWS và thực hành chạy WordPress trên EC2.  
* Sẵn sàng kết hợp cả 3 mô hình (Serverless – Elastic Beanstalk – EC2) cho các dự án thực tế.

