---
title: "Worklog Tuần 9 – Application Modernization on AWS"

weight: 1
chapter: false
pre: " <b> 1.x. </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây là bản mẫu tham khảo — không sao chép nguyên văn cho bài nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Hiểu cách hiện đại hóa ứng dụng với Microservices, Serverless và Event-driven.
* Biết cách chuyển đổi Monolith → Microservices và thiết lập CI/CD cho ứng dụng hiện đại.
* Học và thực hành các series Serverless (DevAx, Book Store, Document Management System).
* Nắm được API Gateway, SAM, Cognito, AppSync, CloudFront, Amplify, DynamoDB trong kiến trúc modern.

---

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                 | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------ |
| 2   | - Overview **Application Modernization on AWS** <br> - Monolith to Microservices Migration                                           | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - CI/CD for Application Release <br> - Building Microservices <br> - Data Restructuring & Workflow Restructuring                     | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - Event-Driven Architecture <br> - SPA Authentication (Cognito) <br> - AWS AI Services Integration                                   | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - Serverless - DevAx Series (Backend & Architecture)                                                                                 | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - Serverless Book Store Series: Lambda + S3 + DynamoDB <br> - Frontend for Serverless APIs <br> - Deployment Automation with AWS SAM | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 7   | - Cognito Authentication <br> - Custom Domains + SSL <br> - Event Processing with SQS/SNS <br> - CI/CD for Serverless Applications   | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 8   | - Monitoring Serverless Apps <br> - AppSync GraphQL APIs <br> - Serverless Document Management System Series                         | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 9   | - Document CRUD with Lambda + DynamoDB <br> - Amplify Storage/Auth <br> - API Gateway Frontend Integration                           | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 10  | - Deployment with AWS SAM <br> - CloudFront setup <br> - Adding Search functionality                                                  | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 11  | - DevOps for Document System <br> - Distributed Tracing: X-Ray + CloudWatch                                                          | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |


---

### Kết quả đạt được:

#### 🏗 1. Application Modernization Fundamentals
- Nắm vững mô hình Monolith → Microservices.  
- Hiểu cách refactor dữ liệu, workflow và kiến trúc khi chuyển sang microservices.  
- Biết cách xây dựng CI/CD giúp release ứng dụng nhanh và ổn định.

#### ⚡ 2. Event-Driven & Serverless Architecture
- Event-driven với S3, SNS, SQS, Lambda, EventBridge.  
- SPA Authentication bằng Amazon Cognito (User Pool, Identity Pool).  
- Tích hợp AWS AI Services vào ứng dụng (Rekognition, Comprehend…).

---

### 🟣 **Serverless – DevAx Series**
- Kiến trúc backend serverless: Lambda, API Gateway, DynamoDB.  
- Cách xây dựng ứng dụng scalable, không quản lý server.  
- Tối ưu performance & lỗi bằng event-driven patterns.

---

### 🟢 **Serverless – Book Store Series**

#### Backend
- Lambda + S3 + DynamoDB cho CRUD và xử lý nghiệp vụ.  
- API Gateway routing request vào Lambda.

#### Frontend
- Xây dựng giao diện gọi API serverless.  
- Quản lý state, fetch, re-render tối ưu.

#### Deployment
- Triển khai với **AWS SAM**: template.yaml, build & deploy tự động.  
- CI/CD hoàn chỉnh cho serverless apps.

#### Authentication
- Dùng **Amazon Cognito** cho sign-in, sign-up, token JWT.

#### Advanced
- Custom domain + SSL bằng ACM + API Gateway.  
- Event processing với SNS & SQS.  
- Monitoring bằng CloudWatch: logs, metrics, alarms.  
- GraphQL APIs bằng **AWS AppSync**.

---

### 🔵 **Serverless – Document Management System Series**

#### Backend & CRUD
- Lambda + DynamoDB cho CRUD document.  
- Tối ưu partition key, GSI.

#### Storage & Auth
- AWS Amplify: Authentication + Storage + Hosting.  
- Upload & download document an toàn.

#### Integration
- API Gateway liên kết frontend → backend.  
- CloudFront phân phối nội dung nhanh, bảo mật.

#### Search & Optimization
- Thêm search engine (ví dụ: DynamoDB Scan/Query hoặc OpenSearch).  
- Tối ưu latency và throughput cho hệ thống.

#### DevOps & Monitoring
- Deployment với AWS SAM.  
- Distributed tracing với **X-Ray + CloudWatch**.  
- Hoàn thiện DevOps pipeline cho Document System.

---

### Tổng kết tuần:

* Hiểu toàn diện về hiện đại hóa ứng dụng trên AWS (Microservices + Serverless).  
* Thành thạo nhiều công nghệ: Lambda, SAM, DynamoDB, Amplify, AppSync, CloudFront, Cognito…  
* Nắm được cách thiết kế, triển khai, theo dõi và mở rộng ứng dụng hiện đại.  
* Sẵn sàng áp dụng kiến trúc Modern Application cho dự án thực tế.

