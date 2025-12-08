---
title: "Bản đề xuất"
date: "2025-12-04T07:05:17Z"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

{{% notice warning %}}
 **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

# Nền tảng Devteria Game Store
## Giải pháp E-commerce trên AWS Cloud cho phân phối game số

### Tài liệu Dự án

📄 [Dự Án Cửa Hàng Game Devteria Với AWS (Word)](/downloads/AWS_Project_Plan_Devteria.docx)

---

### 1. Tóm tắt điều hành
Devteria Game Store là nền tảng thương mại điện tử có khả năng mở rộng dành cho việc mua và quản lý bản quyền game kỹ thuật số. Được xây dựng trên AWS cloud, nền tảng cung cấp xác thực bảo mật, quản lý tồn kho thời gian thực, xử lý đơn hàng tự động và phân phối nội dung toàn cầu. Hỗ trợ hàng nghìn người dùng đồng thời với độ sẵn sàng cao (99.9% uptime) và hiệu quả chi phí thông qua kiến trúc serverless.

### 2. Tuyên bố vấn đề

**Thách thức hiện tại:**
- Cửa hàng game truyền thống gặp khó khăn với lưu lượng truy cập tăng đột biến
- Quy trình xác thực và thanh toán phức tạp giảm tỷ lệ chuyển đổi
- Quản lý tồn kho thủ công dẫn đến bán vượt số lượng
- Thiếu phân tích thời gian thực để đưa ra quyết định
- Chi phí cơ sở hạ tầng cao cho công suất đỉnh

**Giải pháp:** Devteria tận dụng các dịch vụ AWS: CloudFront + S3 (phân phối nội dung nhanh), Cognito (xác thực an toàn), API Gateway + Lambda (backend serverless), RDS + S3 (lưu trữ đáng tin cậy), SQS + SNS (xử lý bất đồng bộ), và CodePipeline (CI/CD tự động).

### 3. Kiến trúc giải pháp

![Kiến trúc Devteria](/images/2-Proposal/proposal.jpg)

**Các thành phần chính:**
- **Frontend**: CloudFront CDN + S3 (ứng dụng React, bộ nhớ đệm toàn cầu, tải <2s)
- **Backend**: API Gateway + Lambda (logic nghiệp vụ tự động mở rộng) + ALB + EC2 (microservices)
- **Dữ liệu**: RDS PostgreSQL (người dùng, danh mục, đơn hàng) + S3 (tệp game, tài sản) + SQS/SNS (xử lý bất đồng bộ)
- **Bảo mật**: Cognito (xác thực với MFA) + IAM (kiểm soát truy cập) + CloudWatch (giám sát)
- **CI/CD**: GitLab → CodePipeline → CodeBuild → Triển khai

**Luồng người dùng:** Truy cập site → Đăng nhập (Cognito) → Duyệt game (API/Lambda/RDS) → Thêm vào giỏ → Thanh toán → Tạo bản quyền (SQS) → Email (SNS) → Tải xuống an toàn (S3)

### 4. Dịch vụ AWS

| Dịch vụ | Mục đích | Cấu hình |
|---------|---------|----------|
| CloudFront | CDN | 10M request, 50GB transfer |
| S3 | Lưu trữ | 100GB (frontend + tài sản) |
| API Gateway | Quản lý API | 1M request/tháng |
| Lambda | Serverless Compute | 5M invocations, 512MB |
| EC2 | Microservices | 2x t3.medium |
| RDS | Database | db.t3.small Multi-AZ |
| ALB | Load Balancer | 1 ALB |
| Cognito | Xác thực | 10K người dùng |
| SQS + SNS | Queue + Thông báo | 5M + 100K tin nhắn |
| CloudWatch | Giám sát | Số liệu + nhật ký |
| CodePipeline | CI/CD | 1 pipeline |

### 5. Lộ trình triển khai (6 tháng)

| Tháng | Mốc quan trọng |
|-------|----------------|
| **1** | Cơ sở hạ tầng: Thiết lập AWS, VPC, RDS, S3, Cognito |
| **2-3** | Backend: Lambda APIs (xác thực, danh mục, đơn hàng) + API Gateway |
| **3** | Frontend: Ứng dụng React/Next.js + tích hợp Cognito |
| **4** | Nâng cao: Cổng thanh toán + Bảng quản trị + CI/CD |
| **5** | Kiểm thử: Load tests + Kiểm toán bảo mật + Tối ưu hiệu suất |
| **6** | Ra mắt: Ra mắt thử nghiệm → Ra mắt công khai |

### 6. Ước tính ngân sách

**Chi phí hàng tháng (10K người dùng, 1K đơn hàng/tháng): ~$228**

| Dịch vụ | Chi phí |
|---------|---------|
| CloudFront + S3 + API Gateway + Lambda | $32 |
| EC2 (2x t3.medium) + RDS (t3.small) | $110 |
| ALB + Cognito | $50 |
| SQS + SNS + CloudWatch + Khác | $36 |

**Mở rộng:** 50K người dùng (~$650/tháng), 100K người dùng (~$1,200/tháng)

**Một lần:** Phát triển ($5K-8K), Tên miền ($15/năm), SSL (Miễn phí qua ACM)

### 7. Đánh giá rủi ro

| Rủi ro | Giảm thiểu |
|--------|------------|
| Tấn công DDoS | AWS Shield, CloudFront, giới hạn tốc độ |
| Rò rỉ dữ liệu | Mã hóa, IAM, kiểm toán định kỳ |
| Gian lận thanh toán | 3D Secure, phát hiện gian lận |
| Lambda Cold Starts | Provisioned concurrency |
| Vượt chi phí | Cảnh báo ngân sách, giới hạn tự động mở rộng |

**Dự phòng:** Sao lưu tự động RDS, triển khai Multi-AZ, rollback CodePipeline, trang bảo trì tĩnh

### 8. Kết quả kỳ vọng

**Kỹ thuật:**
- Hiệu suất: Tải trang <2s toàn cầu
- Khả năng mở rộng: Xử lý tăng lưu lượng 10x
- Độ tin cậy: 99.9% uptime
- Bảo mật: Không rò rỉ, sẵn sàng PCI

**Kinh doanh:**
- Giảm 40% tỷ lệ bỏ giỏ hàng
- Giảm 60% thời gian quản lý cơ sở hạ tầng
- Tăng 25-35% doanh thu từ UX tốt hơn
- Tiếp cận thị trường toàn cầu qua CDN

**Dài hạn:** Mở rộng đến 100K+ người dùng, nhóm tích lũy kinh nghiệm AWS, microservices tái sử dụng, phát triển tính năng nhanh

---

### Bước tiếp theo
1. Phê duyệt đề xuất
2. Thiết lập tài khoản AWS
3. Tập hợp nhóm
4. Bắt đầu Giai đoạn 1
5. Đánh giá tiến độ hàng tuần