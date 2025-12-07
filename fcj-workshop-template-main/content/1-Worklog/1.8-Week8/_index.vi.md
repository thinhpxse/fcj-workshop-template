---
title: "Worklog Tuần 8– AWS Cost Optimization"

weight: 1
chapter: false
pre: " <b> 1.x. </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây chỉ là bản mẫu tham khảo, không sao chép nguyên văn cho bài nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Hiểu được các nguyên tắc tối ưu chi phí trong AWS Well-Architected Framework.
* Xác định các lựa chọn giảm chi phí dài hạn: Savings Plans, Reserved Instances (RI).
* Trực quan hóa và phân tích chi phí bằng Cost Explorer & Cost and Usage Reports.
* Biết cách sử dụng Glue + Athena để phân tích dữ liệu chi phí chuyên sâu.

---

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                    | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------ | --------------- | ------------------------------------------ |
| 2   | - Tổng quan **AWS Cost Optimization** <br> - 5 nguyên tắc tối ưu chi phí trong AWS                                      | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - Tìm hiểu **Savings Plans** và **Reserved Instances (RI)** <br> - So sánh Savings Plans Compute vs EC2 RI              | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - Cost Visualization với AWS Cost Explorer <br> - Cost & Usage Report (CUR)                                             | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - Phân tích chi phí bằng các biểu đồ, trends, Tag-based cost allocation                                                 | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - Tạo ETL pipeline với **AWS Glue** để xử lý Cost & Usage Report                                                         | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 7   | - Query dữ liệu chi phí bằng **Amazon Athena** <br> - Xây dựng dashboard phân tích chi phí nâng cao                     | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |

---

### Kết quả đạt được:

#### 💰 1. Cost Savings (Tiết kiệm chi phí)
- Hiểu rõ sự khác nhau giữa:
  - **Savings Plans**: linh hoạt, áp dụng cho nhiều service (EC2, Fargate, Lambda).  
  - **Reserved Instances (RI)**: rẻ hơn nhưng gắn với instance type/region cụ thể.  
- Biết cách lựa chọn Saving Plans 1 năm hoặc 3 năm tùy mức độ cam kết.

#### 📊 2. Cost Visualization & Analytics
- Sử dụng **Cost Explorer** để:
  - Xem mức tiêu thụ theo ngày/tháng.
  - Phân loại chi phí theo service, tag, account.
  - Phát hiện spike + bất thường trong chi phí.

- Tạo **Cost and Usage Report (CUR)** để theo dõi toàn bộ dữ liệu chi tiết.

#### 🔍 3. Cost Data Analysis with Glue & Athena
- Tạo Glue Crawler để index dữ liệu chi phí từ CUR.
- Query CUR bằng Athena:
  - Phân tích chi phí theo tag/product.
  - Tính toán tổng chi phí theo EC2, S3, RDS…
  - Xây dựng báo cáo custom theo nhu cầu.

- Hiểu cách sử dụng Athena + Glue như một bộ BI mini để phân tích cost realtime.

---

### Tổng kết tuần:

* Hiểu sâu cách tối ưu chi phí trong AWS từ cơ bản đến nâng cao.  
* Biết cách sử dụng Savings Plans và RI để tối ưu lâu dài.  
* Thành thạo cơ bản Cost Explorer, CUR, Glue và Athena cho việc phân tích chi phí.  
* Có khả năng xây dựng dashboard cost analytics giúp doanh nghiệp tối ưu ngân sách cloud.  

