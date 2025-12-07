---
title : "Tổng quan Workshop"
date :  2025-12-06
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

#### Tìm hiểu về VPC Endpoints

**VPC Endpoints** cho phép kết nối riêng tư giữa Virtual Private Cloud (VPC) của bạn và các dịch vụ AWS mà không yêu cầu lưu lượng phải đi qua internet công cộng. Các endpoints này có đặc điểm:
- **Độ sẵn sàng cao**: Được xây dựng với tính dự phòng trên nhiều Availability Zones
- **Khả năng mở rộng**: Tự động mở rộng để xử lý nhu cầu workload của bạn
- **Bảo mật**: Giữ lưu lượng trong mạng AWS, giảm thiểu rủi ro từ các mối đe dọa trên internet

Có hai loại VPC endpoints chính:
1. **Gateway Endpoints**: Được sử dụng để kết nối với Amazon S3 và DynamoDB. Chúng được chỉ định làm đích trong bảng định tuyến cho lưu lượng đến các dịch vụ AWS được hỗ trợ.
2. **Interface Endpoints (AWS PrivateLink)**: Cho phép kết nối riêng tư đến các dịch vụ được hỗ trợ bởi AWS PrivateLink, bao gồm nhiều dịch vụ AWS, dịch vụ của riêng bạn, và các ứng dụng SaaS. Chúng tạo các elastic network interfaces (ENIs) trong subnets của bạn.

#### Tổng quan Kiến trúc Lab

Workshop thực hành này minh họa cách truy cập an toàn vào Amazon S3 từ nhiều môi trường mạng khác nhau bằng cách sử dụng VPC endpoints. Kiến trúc lab bao gồm hai Virtual Private Clouds riêng biệt:

**Môi trường Cloud VPC:**
- Lưu trữ các tài nguyên AWS cloud-native bao gồm các EC2 instances để kiểm thử
- Chứa **Gateway VPC Endpoint** cung cấp quyền truy cập riêng tư, trực tiếp vào Amazon S3
- Đại diện cho cơ sở hạ tầng AWS cloud production của bạn
- Minh họa cách các workload cloud có thể truy cập S3 một cách an toàn mà không cần phơi bày ra internet

**VPC Mô phỏng On-Premises:**
- Mô phỏng môi trường trung tâm dữ liệu truyền thống hoặc văn phòng chi nhánh
- Bao gồm một EC2 instance được cấu hình với phần mềm VPN (OpenSwan/strongSwan) để thiết lập kết nối bảo mật
- Kết nối với AWS thông qua **Site-to-Site VPN** qua AWS Transit Gateway
- Có **Interface VPC Endpoint** cho phép các tài nguyên on-premises truy cập S3 một cách riêng tư qua kết nối VPN
- Mô phỏng các kịch bản hybrid cloud nơi các ứng dụng on-premises cần truy cập S3 một cách an toàn

**Kết nối Mạng:**
Hai VPC được kết nối với nhau thông qua **AWS Transit Gateway**, hoạt động như một cloud router để cho phép giao tiếp an toàn giữa các môi trường cloud và on-premises mô phỏng của bạn. Kết nối Site-to-Site VPN đảm bảo luồng lưu lượng được mã hóa giữa các môi trường.

**Lưu ý quan trọng:**
- Lab này sử dụng một đường hầm VPN duy nhất để tiết kiệm chi phí và đơn giản hóa
- Đối với các triển khai production, AWS khuyến nghị mạnh mẽ việc triển khai các kết nối VPN dự phòng trên nhiều thiết bị và Availability Zones để đảm bảo độ sẵn sàng cao
- Kiến trúc tuân theo các nguyên tắc của AWS Well-Architected Framework về bảo mật và độ tin cậy

![Sơ đồ Kiến trúc Workshop](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

Workshop này sẽ hướng dẫn bạn cấu hình cả Gateway và Interface VPC Endpoints, kiểm tra kết nối từ cả hai môi trường, và hiểu các best practices để bảo mật quyền truy cập S3 trong kiến trúc hybrid cloud.
