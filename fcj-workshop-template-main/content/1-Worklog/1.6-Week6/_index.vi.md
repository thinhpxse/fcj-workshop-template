---
title: "Week 6"
weight: 1
chapter: false
pre: " <b> 1.6 </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây chỉ là bản mẫu tham khảo, không sao chép nguyên văn cho bài nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Nắm được các nguyên tắc Reliability trong AWS Well-Architected Framework.
* Hiểu cách xây dựng hệ thống resilient: backup, failover, HA, network reliability.
* Thực hành các giải pháp tăng độ sẵn sàng cho hệ thống: Backup, Multi-Attach, SQS/SNS, Clustering.
* Nhận biết các mô hình High Availability dành cho Database & Windows Server.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                    | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------ | --------------- | ------------------------------------------ |
| 2   | - Tổng quan **AWS Reliability** <br> - Nguyên tắc: Fault tolerance, High Availability, Backup, Failover                                                     | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - **AWS Backup**: cấu hình plan, vault, backup rule <br> - Tìm hiểu các chiến lược bảo vệ dữ liệu                                                            | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - Network Reliability: **VPC Peering** <br> - **Transit Gateway** để quản lý mạng tập trung                                                                  | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - Messaging Systems: **SQS** (queue), **SNS** (pub-sub) <br> - Ứng dụng messaging cho hệ thống phân tán                                                      | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - Shared Storage với **Amazon EBS Multi-Attach** <br> - Database HA using Multi-Attach + Systems Manager                                                     | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 7   | - **Windows Server Failover Clustering (WSFC)** trên AWS <br> - Kiến trúc Failover nâng cao                                                                  | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 8   | - SQL Server High Availability trên AWS: <br> &emsp; + Phiên bản 2019 <br> &emsp; + Phiên bản 2022                                                           | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |


### Kết quả đạt được:

#### 🛡 Data Protection (Bảo vệ dữ liệu)
- Hiểu cách sử dụng **AWS Backup** để quản lý backup tập trung.  
- Cấu hình backup plan, vault, lifecycle, cross-region backup.  
- So sánh AWS Backup với snapshot thủ công.

#### 🌐 Network Reliability
- **VPC Peering**: kết nối 2 VPC với độ trễ thấp, không đi qua Internet.  
- **AWS Transit Gateway**: quản lý hàng trăm VPC & on-prem trong mô hình hub-and-spoke.  
- Nắm được cách thiết kế mạng High Availability.

#### 📩 Messaging for Reliability
- **Amazon SQS**: xử lý bất đồng bộ, giảm tải hệ thống, tránh mất message.  
- **Amazon SNS**: publish-subscribe, fan-out kiến trúc microservices.  
- Ứng dụng messaging để xây dựng hệ thống resilient & decoupled.

#### 💾 Storage Reliability
- **EBS Multi-Attach**: chia sẻ volume giữa nhiều instance → tăng HA cho ứng dụng cần shared disk.  
- Sử dụng Systems Manager automation để cấu hình HA cho database.

#### 🪟 Windows & SQL Server High Availability
- **Windows Server Failover Clustering (WSFC)** trên AWS: shared storage, failover node.  
- **SQL Server HA 2019 & 2022** trên AWS:  
  - Always On Availability Groups  
  - Multi-AZ deployment  
  - Automatic failover  

### Tổng kết tuần:

* Hiểu rõ cách tăng độ tin cậy hạ tầng AWS từ storage, network đến messaging.  
* Biết cách triển khai HA cho hệ thống Windows & SQL Server.  
* Có nền tảng để xây dựng kiến trúc AWS đạt chuẩn Reliability trong Well-Architected Framework.  
* Sẵn sàng áp dụng các mô hình HA/DR vào dự án thực tế trong môi trường Production.

