---
title: "Worklog Tuần 4 – Optimizing the System on AWS (Operations)"

weight: 1
chapter: false
pre: " <b> 1.x. </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây chỉ là bản mẫu tham khảo, không sao chép nguyên văn cho bài nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Hiểu các phương pháp tối ưu hệ thống khi vận hành trên AWS.
* Nắm được các nhóm công cụ Operations: Monitoring, Automation, IaC, Resource Optimization, Cost Management.
* Thực hành cơ bản với CloudWatch, Systems Manager, IAM, Resource Tags, và Amazon EBS automation.
* Sẵn sàng cho việc tối ưu hiệu suất, chi phí, và bảo mật trong môi trường AWS thực tế.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------ |
| 2   | - Đọc tổng quan mục **Optimizing the System on AWS** <br> - Giải thích vai trò của Operations trong AWS                                                                      | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - Tìm hiểu **Serverless Automation với AWS Lambda** <br> - **Monitoring nâng cao** với CloudWatch & Grafana                                                                  | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - Workshop CloudWatch Advanced (Logs, Metrics, Alarms) <br> - Quản lý tài nguyên với **Tags và Resource Groups**                                                             | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - IAM & Resource Tags để kiểm soát truy cập chi tiết <br> - Systems Management với **AWS Systems Manager** <br> - Truy cập máy chủ bằng **Session Manager**                  | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - Tìm hiểu Infrastructure as Code với **AWS CloudFormation** <br> - Học AWS CDK (Essentials + Advanced) <br> - Resource Optimization với EC2                                  | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 7   | - Network Monitoring với **VPC Flow Logs** <br> - Cost & Usage Management <br> - EBS Snapshot Automation với Amazon EBS DLM <br> - Anomaly Detection for EBS Backups          | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |

### Kết quả đạt được:

* Nắm được vai trò **Operations** trong tối ưu hệ thống:
  - Tự động hóa (Lambda, Systems Manager)
  - Giám sát nâng cao (CloudWatch, Grafana)
  - Quản lý tài nguyên chuẩn hóa bằng Tags
  - Infrastructure as Code (CloudFormation, CDK)
  - Tối ưu chi phí & hiệu suất (EC2 Right-Sizing, Billing Tools)
  - Tối ưu độ tin cậy & backup (EBS, DLM, Snapshot Automation)

* Đã hiểu chi tiết từng dịch vụ chính trong tuần:  
  - **AWS Lambda**: Automations, event-driven tasks  
  - **CloudWatch & Grafana**: Dashboard, metrics, logs, anomaly detection  
  - **IAM + Resource Tags**: Fine-grained permissions  
  - **Systems Manager + Session Manager**: Quản lý & truy cập server không cần SSH  
  - **CloudFormation & AWS CDK**: Triển khai hạ tầng lặp lại, có version control  
  - **EC2 Resource Optimization**: Right-sizing, kiểm tra instance dư thừa  
  - **VPC Flow Logs**: Theo dõi network traffic phục vụ bảo mật & debugging  
  - **Cost and Usage Management**: Theo dõi, cảnh báo chi phí  
  - **Amazon EBS Lifecycle Manager**: Tự động hóa snapshot theo lịch

* Hoàn thành ghi chú và phân tích về tối ưu hệ thống:  
  - Tối ưu hiệu năng  
  - Tối ưu chi phí  
  - Tối ưu bảo mật  
  - Tối ưu quản trị và tự động hóa  

* Chuẩn bị kiến thức dành cho tuần tiếp theo: **Security Optimization / Performance Optimization / Cost Optimization**.

