---
title: "Week 1"

weight: 1
chapter: false
pre: " <b> 1.1 </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

### Nguồn tài liệu tham khảo
Nội dung tóm tắt dựa trên mục **Explore AWS Services** của Cloud Journey - AWS Study Group. citeturn0search0

---
## Tuần 1

### Mục tiêu tuần 1
* Xây dựng nền tảng AWS: tạo tài khoản, hiểu chi phí và cơ chế hỗ trợ.  
* Nắm vững khái niệm cơ bản về quản lý truy cập (IAM), mạng (VPC) và compute (EC2).  
* Thực hành: tạo tài khoản, cấu hình IAM, deploy EC2, dùng Cloud9, host website tĩnh trên S3 và tạo database RDS cơ bản.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc (chi tiết)                                                                                                                                                                                                                  | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | **Creating Your First AWS Account** <br> - Đăng ký AWS Free Tier (tạo root user) <br> - Bật MFA cho root, thêm thông tin thanh toán, tạo billing alarm để tránh phí bất ngờ.                                                  | TBD     08/09/2025     | TBD       08/09/2025      | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 2   | **Managing Costs with AWS Budgets** <br> - Thiết lập Budget, alert (email/SNS), xem Cost Explorer cơ bản; thực hành tạo budget cho dự án nhỏ.                                                                                 | TBD    09/09/2025	      | TBD      09/09/2025	       | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 3   | **Getting Help with AWS Support** <br> - Hiểu các loại support plans, cách tạo support case, dùng documentation & community (AWS Study Group) để debug issues.                                                                   | TBD     10/09/2025     | TBD      10/09/2025       | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 3   | **Access Management (IAM)** <br> - Tạo IAM user/group, policy cơ bản, thực hành principle of least privilege, tạo role cho dịch vụ.                                                                                         | TBD     10/09/2025     | TBD       10/09/2025      | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 4   | **Networking Essentials (VPC)** <br> - Tạo VPC, subnet (public/private), route table, Internet Gateway, NAT Gateway; hiểu CIDR, security group vs NACL.                                                                          | TBD    10/09/2025      | TBD       10/09/2025      | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 4   | **Compute Essentials (EC2)** <br> - Khởi tạo EC2 instance (AMI, instance type), SSH keypair, security group; quản lý EBS volume; snapshot cơ bản.                                                                            | TBD     11/09/2025     | TBD     11/09/2025        | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 5   | **Instance Profiling with IAM Roles for EC2** <br> - Tạo IAM Role cho EC2, gán policy để instance có thể truy cập S3/RDS/CloudWatch mà không cần access key.                                                                | TBD     11/09/2025     | TBD     11/09/2025        | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 5   | **Cloud Development with AWS Cloud9** <br> - Mở Cloud9 IDE, làm quen workspace, deploy ứng dụng nhỏ (ví dụ: node/python) từ Cloud9 lên EC2 hoặc S3.                                                                            | TBD     11/09/2025     | TBD      11/09/2025       | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 6   | **Static Website Hosting with Amazon S3** <br> - Tạo bucket, cấu hình hosting tĩnh, thiết lập policy để public (hoặc dùng CloudFront để bảo mật), thử deploy trang HTML/CSS đơn giản.                                          | TBD     12/09/2025     | TBD      12/09/2025       | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 6   | **Database Essentials (Amazon RDS)** <br> - Tạo RDS instance (MySQL/Postgres), cấu hình subnet group, security group, backup window; test kết nối từ EC2.                                                                      | TBD     12/09/2025     | TBD      12/09/2025       | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 7   | **Simplified Computing (Amazon Lightsail)** <br> - Khám phá cách dùng Lightsail để triển khai nhanh VM hoặc app, so sánh chi phí/thuận tiện với EC2.                                                                            | TBD    12/09/2025      | TBD       12/09/2025      | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 7   | **Container Deployment with Amazon Lightsail Containers** <br> - Thử deploy container đơn giản trên Lightsail Containers để hiểu workflow: image push, deploy, public endpoint.                                              | TBD     12/09/2025     | TBD       12/09/2025      | cloudjourney.awsstudygroup.com. citeturn0search0 |

### Kết quả đạt được tuần 1:
* Đã tạo và bảo mật AWS account (MFA, billing alarm), hiểu cơ bản về kế hoạch chi phí và support.  
* Nắm được nguyên lý IAM, đã tạo user/role/policy thử nghiệm.  
* Thiết lập VPC đơn giản, deploy EC2 và kết nối SSH; đã gắn EBS và snapshot thử.  
* Dùng Cloud9 để phát triển nhanh và host trang tĩnh trên S3.  
* Tạo RDS instance cơ bản và kiểm tra kết nối; thử deploy app đơn giản trên Lightsail và Lightsail Containers.
