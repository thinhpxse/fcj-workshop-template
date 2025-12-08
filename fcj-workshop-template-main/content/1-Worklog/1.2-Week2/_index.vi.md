---
title: "Week 2"

weight: 1
chapter: false
pre: " <b> 1.2 </b> "
---
{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}


### Mục tiêu tuần 2
* Nâng cao khả năng vận hành: autoscaling, monitoring, DNS, CDN, CLI và cơ sở dữ liệu NoSQL / caching.  
* Thực hành triển khai hệ thống có khả năng scale & high-availability cơ bản, sử dụng CloudWatch để giám sát và Route53 để cấu hình DNS.  
* Làm quen với DynamoDB, ElastiCache, CloudFront, Lambda@Edge, và quản trị Windows/Directory trên AWS (cơ bản).

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc (chi tiết)                                                                                                                                                                                                                  | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | **Scaling Applications with EC2 Auto Scaling** <br> - Tạo Launch Template/Configuration, Auto Scaling Group (ASG), policy scale-out/scale-in; test bằng cách tăng tải giả lập.                                                   | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 2   | **Monitoring with Amazon CloudWatch** <br> - Tạo CloudWatch Alarm, metrics custom, log group; cấu hình dashboard để theo dõi CPU, network, health check.                                                                            | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 3   | **Hybrid DNS Management with Amazon Route 53** <br> - Tạo hosted zone, record set (A/CNAME/ALIAS), health check; kết hợp DNS nội bộ với public DNS nếu cần.                                                                      | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 3   | **Command Line Operations with AWS CLI** <br> - Cài đặt & cấu hình AWS CLI, thực hành EC2/S3/RDS/DynamoDB commands, script nhỏ tự động hóa thao tác thường xuyên.                                                                 | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 4   | **NoSQL Database Essentials (Amazon DynamoDB)** <br> - Tạo table, design partition key & sort key, đọc/ghi item, test throughput (read/write capacity / on-demand).                                                                 | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 4   | **In‑Memory Caching with Amazon ElastiCache** <br> - Triển khai Redis/Memcached; tích hợp cache cho ứng dụng; cấu hình cluster replication (nếu cần) và test latency giảm.                                                       | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 5   | **Networking on AWS Workshop** <br> - Bài tập nâng cao về VPC, peering, VPN/Direct Connect khái quát, kiểm tra flow logs và network troubleshooting.                                                                            | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 5   | **Content Delivery with Amazon CloudFront** <br> - Tạo distribution, origin (S3/ALB), invalidation, kiểm tra TTL; dùng CloudFront để tăng tốc static assets.                                                                     | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 6   | **Edge Computing with CloudFront and Lambda@Edge** <br> - Viết function Lambda@Edge nhỏ để thay đổi headers/redirect; hiểu use-cases edge compute.                                                                               | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 6   | **Windows Workloads on AWS** <br> - Triển khai EC2 Windows instance, license model, RDP access, cấu hình patching & backup cơ bản.                                                                                                | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 7   | **Directory Services (AWS Managed Microsoft AD)** <br> - Khởi tạo AWS Managed Microsoft AD, nối domain cho EC2 Windows, test authentication và group policy đơn giản.                                                              | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |
| 7   | **Building Highly Available Web Applications** <br> - Thiết kế multi-AZ cho EC2/RDS, load balancing (ALB), health checks, tự động scale bằng ASG, sử dụng CloudFront + Route53 để tăng HA và hiệu năng.                           | TBD          | TBD             | cloudjourney.awsstudygroup.com. citeturn0search0 |

### Kết quả đạt được tuần 2:
* Hiểu và cấu hình Auto Scaling cho ứng dụng EC2; có thể mô phỏng scale-out/scale-in.  
* Thiết lập CloudWatch metrics/alarms và dashboard để giám sát cơ bản; biết cách thu thập log.  
* Cấu hình DNS với Route53, sử dụng CloudFront để tối ưu phân phối nội dung; thử nghiệm Lambda@Edge đơn giản.  
* Triển khai DynamoDB và ElastiCache; hiểu trade-offs giữa RDBMS / NoSQL / cache.  
* Quản trị workload Windows cơ bản và thử nghiệm AWS Managed Microsoft AD cho authentication.  

---
