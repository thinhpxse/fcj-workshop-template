---
title: "Worklog  5 – AWS Security"
weight: 1
chapter: false
pre: " <b> 1.x. </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây chỉ là bản mẫu tham khảo, không sao chép nguyên văn cho bài nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Hiểu các nguyên tắc bảo mật trong AWS: Identity, Access, Data Protection, Threat Detection.
* Nắm vai trò của IAM, KMS, VPC, Firewall Manager, GuardDuty và các dịch vụ Security khác.
* Thực hành tạo quyền, bảo vệ dữ liệu, quản lý thông tin định danh, và phát hiện mối đe dọa.
* Chuẩn bị kiến thức về Security Best Practices trong môi trường AWS thực tế.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                          | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------ |
| 2   | - Tổng quan chuyên mục **AWS Security** <br> - Xác định 4 nhóm chính: Identity, Access Control, Data Protection, Threat Detection                 | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - Identity Federation với **AWS Single Sign-On (IAM Identity Center)** <br> - IAM Permission Boundaries                                             | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - IAM Policies và IAM Conditions <br> - Security & Compliance với **AWS Security Hub**                                                              | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - Private Access to S3 qua **VPC Endpoints** <br> - Application Protection với **AWS WAF**                                                          | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - Encryption với **AWS KMS** <br> - Data Protection bằng **Amazon Macie** <br> - Secrets Management bằng **AWS Secrets Manager**                   | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 7   | - Firewall Manager cho Security Governance <br> - Threat Detection với **AWS GuardDuty** <br> - EC2 Image Builder cho hệ thống Patching            | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 8   | - Cross-Domain Authentication với **Amazon Cognito** <br> - Tổng hợp S3 Security Best Practices                                                     | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |


### Kết quả đạt được:

* Hiểu chi tiết từng thành phần bảo mật:

#### 🔐 Identity & Access Management
- **AWS Single Sign-On (IAM Identity Center)**: quản lý danh tính tập trung, hỗ trợ SAML/OIDC.  
- **IAM Permission Boundaries**: tạo giới hạn tối đa cho quyền được cấp.  
- **IAM Policies & Conditions**: kiểm soát truy cập theo điều kiện (IP, MFA, thời gian…).  
- **Amazon Cognito**: xác thực người dùng ứng dụng, hỗ trợ OAuth2, IdP, social login.

#### 🔒 Data Protection
- **AWS KMS**: mã hóa dữ liệu, quản lý khóa, rotation.  
- **Amazon Macie**: phát hiện dữ liệu nhạy cảm (PII) trong S3.  
- **S3 Security Best Practices**: Block Public Access, SSE-KMS, Bucket Policies.

#### 🛡 Application & Network Security
- **AWS WAF**: chống SQLi, XSS, bot traffic, rate limiting.  
- **AWS Firewall Manager**: quản trị chính sách bảo mật tập trung.  
- **VPC Endpoints for S3**: truy cập private mà không qua Internet.

#### 🕵 Threat Detection & Monitoring
- **AWS GuardDuty**: phát hiện hành vi bất thường và xâm nhập.  
- **AWS Security Hub**: tổng hợp chuẩn CIS, PCI-DSS, tối ưu tuân thủ bảo mật.  

#### ♻ System Patching
- **EC2 Image Builder**: tự động tạo patch image, giảm lỗ hổng bảo mật.

---

### Tổng kết tuần:

* Đã nắm rõ hệ sinh thái dịch vụ bảo mật AWS.  
* Hiểu cách thiết lập Identity Federation, Permission Boundaries và phân quyền nâng cao.  
* Nắm vững các công cụ mã hóa, giám sát dữ liệu, và bảo mật ứng dụng.  
* Có khả năng xây dựng hệ thống AWS tuân thủ best practice về bảo mật, dữ liệu và quyền truy cập.  
* Sẵn sàng thực hành xây dựng môi trường AWS Secure-by-Design trong các tuần tiếp theo.

