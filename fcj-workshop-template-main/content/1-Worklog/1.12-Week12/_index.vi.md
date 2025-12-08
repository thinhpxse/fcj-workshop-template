---
title: "Week 12"
date: 2024-01-01
weight: 12
chapter: false
pre: " <b> 1.12 </b> "
---
{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

### Mục tiêu tuần 12:

* Ôn tập tất cả các dịch vụ AWS cốt lõi đã học trong suốt thời gian thực tập.
* Thiết kế và triển khai dự án cuối kỳ tích hợp nhiều dịch vụ AWS.
* Chuẩn bị báo cáo cuối kỳ và thuyết trình.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Ôn tập tất cả các dịch vụ cốt lõi: EC2, S3, RDS, DynamoDB, IAM, VPC, Lambda, CloudWatch, CloudFront, API Gateway <br> - Xác định yêu cầu và kiến trúc cho dự án cuối kỳ                                                                                             | 24/11/2025   | 24/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Bắt đầu triển khai dự án: <br>&emsp; + Thiết kế VPC, subnet, security group <br>&emsp; + Cấu hình S3, CloudFront, RDS/DynamoDB (tùy theo dự án)                                            | 25/11/2025   | 25/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tiếp tục triển khai dự án: <br>&emsp; + Xây dựng backend sử dụng Lambda / API Gateway hoặc EC2 (tùy theo kiến trúc) <br>&emsp; + Kết nối database và xử lý dữ liệu <br>&emsp; + Tích hợp CloudWatch để monitoring | 26/11/2025   | 26/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Hoàn thiện dự án: <br>&emsp; + Thêm Cognito authentication nếu cần <br>&emsp; + Hoàn thiện CI/CD pipeline (CodePipeline/CodeBuild) <br>&emsp; + Kiểm thử end-to-end hệ thống                  | 27/11/2025   | 27/11/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Viết báo cáo cuối kỳ <br> - Chuẩn bị bài thuyết trình (kiến trúc, lý do lựa chọn dịch vụ, chi phí, bảo mật) <br> - Tổng kết toàn bộ hành trình học tập và tự đánh giá năng lực                                                                                         | 28/11/2025   | 28/11/2025      | <https://cloudjourney.awsstudygroup.com/> |


### Kết quả đạt được tuần 12:

* Ôn tập Các dịch vụ AWS cốt lõi:
  * EC2, S3, RDS, DynamoDB
  * IAM, VPC, Security Group
  * Lambda, API Gateway
  * CloudWatch, CloudFront
  * Cognito, CodePipeline

* Lập kế hoạch Dự án cuối kỳ:
  * Xác định yêu cầu dự án
  * Thiết kế kiến trúc hệ thống
  * Lựa chọn các dịch vụ AWS phù hợp
  * Tạo sơ đồ kiến trúc
  * Lên kế hoạch các bước triển khai

* Triển khai Hạ tầng Dự án:
  * Thiết kế VPC với public và private subnet
  * Cấu hình security group và network ACL
  * Thiết lập S3 bucket để lưu trữ
  * Cấu hình CloudFront để phân phối nội dung
  * Tạo RDS database instance

* Xây dựng Backend Ứng dụng:
  * Triển khai Lambda function cho business logic
  * Tạo API Gateway REST endpoint
  * Kết nối Lambda với database
  * Cấu hình CloudWatch log và monitoring
  * Thiết lập xử lý lỗi và retry logic

* Hoàn thành Tính năng Dự án:
  * Tích hợp Cognito để xác thực người dùng
  * Thiết lập CodePipeline cho CI/CD
  * Thực hiện kiểm thử end-to-end
  * Sửa lỗi và tối ưu hóa hiệu suất
  * Xác minh tất cả tính năng hoạt động đúng

* Chuẩn bị Bàn giao Cuối kỳ:
  * Viết báo cáo dự án toàn diện
  * Tạo slide thuyết trình bao gồm:
    * Tổng quan kiến trúc
    * Lý do lựa chọn dịch vụ
    * Triển khai bảo mật
    * Ước tính chi phí
  * Tổng kết hành trình học tập 12 tuần
  * Tự đánh giá năng lực kỹ thuật đã đạt được