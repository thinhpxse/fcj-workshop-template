---
title : "Dọn Dẹp Resources và Hoàn Thành Workshop"
date : 2025-12-06
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Chúc mừng bạn đã hoàn thành workshop toàn diện về VPC Endpoints này! Bạn đã thành công khám phá các kiến trúc cấp doanh nghiệp cho kết nối dịch vụ AWS riêng tư.

**Những Gì Bạn Đã Hoàn Thành:**

Trong suốt workshop này, bạn đã triển khai nhiều AWS networking best practices:

✅ **Kiến Trúc Gateway VPC Endpoint**: 
- Tạo Gateway Endpoint để truy cập S3 từ VPC resources
- Cấu hình route table entries cho kết nối S3 riêng tư
- Loại bỏ phụ thuộc Internet Gateway cho S3 traffic
- Triển khai kiểm soát truy cập cấp mạng với endpoint policies

✅ **Interface VPC Endpoint với Hybrid Connectivity**:
- Triển khai Interface Endpoints với tính dự phòng multi-AZ
- Cấu hình Route 53 Resolver cho hybrid DNS resolution
- Thiết lập kết nối dựa trên VPN mô phỏng truy cập on-premises
- Tạo Private Hosted Zones cho endpoint DNS resolution
- Triển khai conditional DNS forwarding rules

✅ **Security Best Practices**:
- Áp dụng restrictive VPC endpoint policies cho least privilege access
- Cấu hình security groups cho endpoint traffic control
- Minh họa defense-in-depth với nhiều lớp policy
- Xác thực policy enforcement qua kiểm tra thực tế

✅ **Production-Ready Skills**:
- Các mẫu triển khai multi-AZ cho high availability
- Thiết kế kiến trúc DNS hybrid cloud
- Kết nối riêng tư không phơi bày internet
- Tối ưu hóa chi phí thông qua sử dụng VPC endpoint

---

#### Tại Sao Dọn Dẹp Resources Rất Quan Trọng

Dọn dẹp resources đúng cách là cần thiết cho:

- **Cost Avoidance**: Ngăn chặn chi phí liên tục cho các resources không sử dụng (VPC endpoints, Route 53 Resolver endpoints, CloudFormation stacks)
- **Account Hygiene**: Duy trì môi trường AWS sạch sẽ cho các dự án tương lai
- **Security Best Practice**: Xóa cơ sở hạ tầng test có thể có cấu hình dễ dãi
- **Service Limits**: Giải phóng service quotas cho production workloads
- **Learning Reinforcement**: Hiểu các phụ thuộc deletion dạy mối quan hệ AWS resource

{{% notice info %}}
**Thời Gian Dọn Dẹp Ước Tính**: 10-15 phút nếu tuân theo thứ tự khuyến nghị. Các phụ thuộc deletion yêu cầu xử lý tuần tự cho một số resources.
{{% /notice %}}

---

#### Thứ Tự Dọn Dẹp và Phụ Thuộc

AWS resources có các phụ thuộc yêu cầu deletion theo thứ tự cụ thể. Tuân theo trình tự này để tránh lỗi:

**Thứ Tự Deletion:**
```
1. Route 53 DNS Records (phụ thuộc Hosted Zone)
2. Route 53 Resolver Rules (phụ thuộc Resolver Endpoints)
3. Route 53 Hosted Zones (phụ thuộc VPCs)
4. VPC Endpoints (phụ thuộc VPCs và Security Groups)
5. S3 Bucket Objects (phải trống trước khi xóa bucket)
6. S3 Buckets (phụ thuộc việc trống)
7. CloudFormation Stacks (xóa VPC infrastructure cuối cùng)
```

---

#### Quy Trình Dọn Dẹp Từng Bước

**Bước 1: Xóa Route 53 Private Hosted Zone DNS Records**

Trước khi xóa hosted zone, xóa các custom DNS records bạn đã tạo.

1. **Điều hướng đến Route 53**:
   - Mở [Route 53 Console](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=us-east-1#)
   - Click **Hosted Zones** trong thanh bên trái

2. **Truy cập Private Hosted Zone**:
   - Click vào hosted zone có tên `s3.us-east-1.amazonaws.com`
   - Bạn sẽ thấy các DNS records bao gồm Alias records bạn đã tạo

3. **Xóa Custom DNS Records**:
   - Chọn **Alias A records** bạn đã tạo (apex và wildcard records trỏ đến Interface Endpoint)
   - KHÔNG chọn các default NS và SOA records (cần thiết cho hosted zone)
   - Click **Delete records**
   - Xác nhận deletion

4. **Xóa Hosted Zone**:
   - Quay lại danh sách Hosted Zones
   - Chọn zone `s3.us-east-1.amazonaws.com`
   - Click **Delete hosted zone**
   - Gõ `delete` trong hộp xác nhận
   - Click **Delete**

![Delete Route 53 Hosted Zone](/images/5-Workshop/5.6-Cleanup/delete-zone.png)

{{% notice warning %}}
**Hosted Zone Deletion**: Bạn không thể xóa hosted zone vẫn còn DNS records (ngoại trừ các NS và SOA records bắt buộc). Đảm bảo bạn đã xóa tất cả custom records trước.
{{% /notice %}}

---

**Bước 2: Xóa Route 53 Resolver Forwarding Rule**

Resolver rule phải được disassociate từ VPCs trước khi deletion.

1. **Điều hướng đến Resolver Rules**:
   - Trong Route 53 console, click **Resolver** > **Rules** trong thanh bên trái

2. **Tìm Forwarding Rule của Bạn**:
   - Tìm rule bạn đã tạo (ví dụ: `S3-PrivateEndpoint-ForwardingRule`)
   - Rule chuyển tiếp `s3.us-east-1.amazonaws.com` queries

3. **Disassociate từ VPC**:
   - Chọn rule
   - Click **View details**
   - Click tab **VPC associations**
   - Chọn **VPC On-prem** được liên kết
   - Click **Disassociate**
   - Xác nhận disassociation

4. **Xóa Rule**:
   - Sau khi disassociate, quay lại danh sách Rules
   - Chọn rule
   - Click **Delete**
   - Xác nhận deletion

![Disassociate and Delete Resolver Rule](/images/5-Workshop/5.6-Cleanup/vpc.png)

{{% notice note %}}
**Rule Dependencies**: Resolver rules phải được disassociate từ tất cả VPCs trước khi deletion. Cố gắng xóa associated rule sẽ dẫn đến lỗi.
{{% /notice %}}

---

**Bước 3: Xóa VPC Endpoints**

Xóa cả Gateway và Interface endpoints bạn đã tạo.

1. **Điều hướng đến VPC Endpoints**:
   - Mở [VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)
   - Click **Endpoints** trong thanh bên trái

2. **Xóa Interface Endpoint**:
   - Chọn **Interface Endpoint** cho S3 (cái trong VPC Cloud)
   - Endpoint type nên hiển thị "Interface"
   - Click **Actions** > **Delete VPC endpoints**
   - Gõ `delete` để xác nhận
   - Click **Delete**

3. **Xóa Gateway Endpoint**:
   - Chọn **Gateway Endpoint** cho S3 (cũng trong VPC Cloud)
   - Endpoint type nên hiển thị "Gateway"
   - Click **Actions** > **Delete VPC endpoints**
   - Gõ `delete` để xác nhận
   - Click **Delete**

{{% notice tip %}}
**Immediate Deletion**: VPC endpoints xóa nhanh (thường trong 1-2 phút). Bạn có thể tiếp tục bước tiếp theo trong khi chúng đang được xóa.
{{% /notice %}}

---

**Bước 4: Làm Trống và Xóa S3 Buckets**

S3 buckets phải hoàn toàn trống trước khi có thể xóa.

1. **Điều hướng đến S3 Console**:
   - Mở [S3 Console](https://s3.console.aws.amazon.com/s3/home?region=us-east-1)

2. **Làm Trống Bucket Đầu Tiên**:
   - Chọn test bucket gốc của bạn (ví dụ: `my-endpoint-test-bucket-123456`)
   - Click **Empty**
   - Gõ `permanently delete` trong hộp xác nhận
   - Click **Empty**
   - Đợi empty operation hoàn thành

3. **Xóa Bucket Đầu Tiên**:
   - Với bucket vẫn được chọn, click **Delete**
   - Gõ tên bucket chính xác để xác nhận
   - Click **Delete bucket**

4. **Lặp Lại cho Bucket Thứ Hai**:
   - Chọn restricted bucket của bạn (ví dụ: `my-endpoint-test-bucket-123456-restricted`)
   - Click **Empty**, xác nhận với `permanently delete`
   - Click **Delete**, gõ tên bucket, xác nhận deletion

![Delete S3 Buckets](/images/5-Workshop/5.6-Cleanup/delete-s3.png)

{{% notice warning %}}
**Cảnh Báo Mất Dữ Liệu**: Làm trống và xóa S3 buckets vĩnh viễn xóa tất cả objects. Đảm bảo bạn không cần bất kỳ test data nào trước khi tiếp tục. Không có khôi phục sau khi xóa.
{{% /notice %}}

---

**Bước 5: Xóa CloudFormation Stacks**

CloudFormation stacks sẽ tự động xóa tất cả resources chúng đã tạo (VPCs, subnets, EC2 instances, Route 53 Resolver endpoints, v.v.).

1. **Điều hướng đến CloudFormation**:
   - Mở [CloudFormation Console](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1)

2. **Xóa PLOnpremSetup Stack**:
   - Chọn stack **PLOnpremSetup**
   - Stack này chứa cơ sở hạ tầng DNS (Resolver endpoints, Private Hosted Zone association)
   - Click **Delete**
   - Xác nhận deletion
   - Deletion mất 3-5 phút

3. **Đợi First Stack Deletion**:
   - Theo dõi stack status - nó sẽ hiển thị "DELETE_IN_PROGRESS"
   - Đợi cho đến khi nó đạt "DELETE_COMPLETE" hoặc biến mất khỏi danh sách
   - Bạn có thể xem deletion events trong tab **Events**

4. **Xóa PLCloudSetup Stack**:
   - Chọn stack **PLCloudSetup**
   - Stack này chứa cơ sở hạ tầng VPC (VPCs, subnets, EC2 instances, VPN gateway, v.v.)
   - Click **Delete**
   - Xác nhận deletion
   - Deletion mất 5-10 phút do số lượng resources

![Delete CloudFormation Stacks](/images/5-Workshop/5.6-Cleanup/delete-stack.png)

{{% notice info %}}
**Stack Deletion Process**: CloudFormation tự động xử lý các phụ thuộc resource trong stack, xóa resources theo thứ tự chính xác. Bạn sẽ thấy các resources riêng lẻ được xóa trong tab Events.
{{% /notice %}}

**Những Gì Được Xóa bởi CloudFormation Stacks:**

**PLOnpremSetup Stack Xóa:**
- Route 53 Inbound Resolver Endpoint (trong VPC Cloud)
- Route 53 Outbound Resolver Endpoint (trong VPC On-prem)
- Các elastic network interfaces liên kết
- IAM roles và policies cho Resolver

**PLCloudSetup Stack Xóa:**
- VPC Cloud và VPC On-prem
- Public và private subnets trong nhiều AZs
- Internet Gateways và NAT Gateways
- Route tables và route table associations
- Security groups
- EC2 instances (Test-Gateway-Endpoint, Test-Interface-Endpoint, VPN Gateway)
- Transit Gateway và attachments
- VPN connections
- Elastic IPs

---

#### Xác Minh Dọn Dẹp Hoàn Toàn

Sau khi hoàn thành tất cả các bước deletion, xác minh rằng resources đã được xóa hoàn toàn:

**Checklist:**

1. **Route 53**:
   - Không còn private hosted zone cho `s3.us-east-1.amazonaws.com`
   - Không còn resolver rules cho S3 domain
   - Inbound và Outbound Resolver endpoints đã biến mất

2. **VPC**:
   - Không còn endpoints trong danh sách Endpoints
   - VPC Cloud và VPC On-prem đã được xóa (sau CloudFormation deletion)
   - Không còn custom security groups liên quan đến workshop

3. **S3**:
   - Cả hai test buckets đã được xóa
   - Không còn objects từ workshop

4. **CloudFormation**:
   - Cả hai stacks hiển thị "DELETE_COMPLETE" hoặc không còn được liệt kê
   - Không có trạng thái deletion thất bại

5. **EC2**:
   - Không còn instances có tên Test-Gateway-Endpoint hoặc Test-Interface-Endpoint
   - Không còn VPN Gateway EC2 instance

{{% notice success %}}
**Dọn Dẹp Hoàn Tất!** Nếu tất cả các mục trong checklist được xác minh, bạn đã thành công xóa tất cả workshop resources. Tài khoản AWS của bạn bây giờ sạch sẽ và bạn sẽ không phải chịu chi phí liên tục cho workshop này.
{{% /notice %}}

---

#### Khắc Phục Sự Cố Dọn Dẹp

**Các Vấn Đề Deletion Thường Gặp:**

**Vấn đề: Hosted Zone Không Xóa Được**
- **Nguyên nhân**: Custom DNS records vẫn còn
- **Giải pháp**: Xóa tất cả A records ngoại trừ NS và SOA records, sau đó thử lại

**Vấn đề: Resolver Rule Không Xóa Được**
- **Nguyên nhân**: Vẫn được liên kết với VPC
- **Giải pháp**: Disassociate từ tất cả VPCs trước, sau đó xóa

**Vấn đề: VPC Endpoint Bị Kẹt Khi Deleting**
- **Nguyên nhân**: Kết nối đang hoạt động hoặc route table associations
- **Giải pháp**: Đợi 5 phút và kiểm tra lại; nên tự động giải quyết

**Vấn đề: S3 Bucket Không Xóa Được**
- **Nguyên nhân**: Objects vẫn còn trong bucket hoặc versioning được bật
- **Giải pháp**: Làm trống bucket hoàn toàn, tắt versioning nếu được bật

**Vấn đề: CloudFormation Stack Deletion Thất Bại**
- **Nguyên nhân**: Manual resource deletion hoặc phụ thuộc ngoài stack
- **Giải pháp**: Kiểm tra tab Events cho lỗi resource cụ thể; có thể cần manually delete blocking resources

---

#### Bài Học Tối Ưu Hóa Chi Phí

**Chi Phí Workshop Resources (nếu để chạy):**

Chi phí giờ ước tính để tham khảo:
- Interface VPC Endpoint: ~$0.01/giờ + $0.01/GB data processed
- Route 53 Resolver Endpoints: ~$0.125/giờ mỗi endpoint (2 endpoints = $0.25/giờ)
- NAT Gateway: ~$0.045/giờ + $0.045/GB data processed
- EC2 instances (t3.micro): ~$0.0104/giờ mỗi instance (3 instances = $0.03/giờ)

**Chi phí hàng tháng nếu không xóa**: ~$200-250

**Điểm Chính**: Luôn xóa test resources ngay lập tức để tránh chi phí không cần thiết!

---

#### Những Gì Bạn Đã Học

**Kỹ Năng Kỹ Thuật:**

- Các mẫu kiến trúc VPC Endpoint (Gateway vs Interface)
- Cấu hình hybrid DNS với Route 53 Resolver
- Kết nối hybrid dựa trên VPN
- Các mẫu high availability multi-AZ
- Triển khai và kiểm tra security policy
- CloudFormation infrastructure as code

**Các Dịch Vụ AWS Đã Thành Thạo:**

- Amazon VPC (Endpoints, Subnets, Route Tables, Security Groups)
- Amazon S3 (Bucket policies, private access)
- Route 53 (Private Hosted Zones, Resolver endpoints, Forwarding rules)
- AWS PrivateLink (Interface Endpoints)
- CloudFormation (Stack management)
- Systems Manager (Session Manager)
- IAM (Policies, roles, endpoint policies)

**Các Mẫu Kiến Trúc Doanh Nghiệp:**

- Defense in depth security
- Least privilege access control
- Network segmentation
- Kết nối riêng tư không phơi bày internet
- Tích hợp hybrid cloud
- Thiết kế high availability

---

#### Các Bước Tiếp Theo để Tiếp Tục Học Tập

**Các Chủ Đề Nâng Cao để Khám Phá:**

1. **AWS Direct Connect**: Thay thế VPN bằng kết nối mạng chuyên dụng
2. **AWS Transit Gateway**: Hub tập trung cho kết nối multi-VPC
3. **VPC Peering**: Mẫu kết nối thay thế giữa các VPCs
4. **PrivateLink for Custom Services**: Expose dịch vụ riêng của bạn qua PrivateLink
5. **Multi-Region Endpoints**: Disaster recovery với cross-region endpoints
6. **AWS Network Firewall**: Kiểm tra và lọc lưu lượng nâng cao
7. **VPC Flow Logs**: Phân tích lưu lượng mạng và giám sát bảo mật

**Tài Liệu AWS Được Khuyến Nghị:**

- [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints.html)
- [Route 53 Resolver](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver.html)
- [AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/privatelink/what-is-privatelink.html)
- [VPC Endpoint Policies](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html)

**Bài Tập Thực Hành:**

- Triển khai các mẫu tương tự cho các dịch vụ AWS khác (RDS, DynamoDB, EC2 API)
- Thiết kế kiến trúc endpoint multi-region
- Tích hợp với AWS Transit Gateway cho enterprise hub-and-spoke
- Triển khai logging và monitoring toàn diện

---

Cảm ơn bạn đã hoàn thành workshop này! Bây giờ bạn có kinh nghiệm thực tế với các mẫu kết nối riêng tư AWS cần thiết cho các kiến trúc cloud doanh nghiệp. Áp dụng những kỹ năng này để xây dựng các giải pháp an toàn, có thể mở rộng và tối ưu chi phí trong các dự án thực tế của bạn!
