---
title : "Tạo Gateway Endpoint cho S3"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Trong phần này, bạn sẽ cấu hình Gateway VPC Endpoint để kích hoạt kết nối riêng tư giữa VPC của bạn và Amazon S3. Điều này loại bỏ nhu cầu về internet gateway, thiết bị NAT, hoặc kết nối VPN, trong khi vẫn giữ lưu lượng truy cập trong mạng AWS.

**Gateway Endpoint là gì?**
Gateway endpoints là các endpoints dựa trên route xuất hiện như các target trong bảng định tuyến VPC của bạn. Khi bạn tạo gateway endpoint cho S3 hoặc DynamoDB, AWS tự động cập nhật các bảng định tuyến được chỉ định với các routes hướng lưu lượng đến endpoint. Cách tiếp cận này:
- Không phát sinh thêm chi phí truyền dữ liệu
- Không yêu cầu elastic network interfaces (ENIs)
- Tự động mở rộng để xử lý workload của bạn

**Lợi ích:**
- **Hiệu quả Chi phí**: Không có phí NAT Gateway cho việc truy cập S3
- **Bảo mật**: Lưu lượng không bao giờ rời khỏi mạng AWS
- **Hiệu suất**: Độ trễ thấp hơn so với truy cập qua internet
- **Kiến trúc Đơn giản**: Không cần quản lý NAT hoặc internet gateway cho việc truy cập S3

---

#### Bước 1: Điều hướng đến VPC Endpoints Console

1. Truy cập [Amazon VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Home:) trong region **us-east-1**
2. Từ menu điều hướng bên trái, tìm và click vào **Endpoints**
3. Click nút **Create endpoint** ở góc trên bên phải

{{% notice info %}}
**Quan sát Dự kiến**: Bạn sẽ thấy **6 VPC endpoints đã tồn tại** trong danh sách. Đây là các **Interface Endpoints** cho các dịch vụ **AWS Systems Manager (SSM)** được tự động cung cấp bởi CloudFormation template trong quá trình thiết lập. Các SSM endpoints này cho phép kết nối riêng tư, an toàn đến Systems Manager mà không cần truy cập internet công cộng, cho phép bạn quản lý EC2 instances thông qua Session Manager.
{{% /notice %}}

![VPC Endpoints Hiện có](/images/5-Workshop/5.3-S3-vpc/endpoints.png)

---

#### Bước 2: Cấu hình Thiết lập Cơ bản cho Endpoint

Trong trang cấu hình **Create endpoint**:

1. **Name tag**: Nhập tên mô tả: `s3-gateway-endpoint`
   - Điều này giúp xác định mục đích của endpoint trong môi trường có nhiều endpoint

2. **Service category**: Chọn **AWS services**
   - Tùy chọn này cho phép bạn chọn từ các service endpoints được AWS quản lý

![Tên Endpoint và Service Category](/images/5-Workshop/5.3-S3-vpc/create-s3-gwe1.png)

---

#### Bước 3: Chọn S3 Gateway Service

1. Trong phần **Services**, sử dụng chức năng tìm kiếm:
   - Gõ `s3` vào ô lọc
   - Danh sách sẽ hiển thị các tùy chọn endpoint liên quan đến S3

2. **Quan trọng**: Chọn service entry có:
   - **Service Name** chứa `com.amazonaws.us-east-1.s3`
   - Cột **Type** hiển thị **Gateway**
   
   KHÔNG chọn endpoint type Interface cho S3.

![Chọn S3 Gateway Service](/images/5-Workshop/5.3-S3-vpc/services.png)

**Hiểu Sự Khác Biệt:**
- **Gateway endpoint** (cái chúng ta đang tạo): Sử dụng các entry trong route table, truyền dữ liệu miễn phí
- **Interface endpoint**: Sử dụng ENIs, phát sinh phí PrivateLink

---

#### Bước 4: Liên kết VPC và Route Tables

1. **Chọn VPC**:
   - Từ menu dropdown VPC, chọn **VPC Cloud**
   - Đây là VPC nơi các tài nguyên cloud của bạn đang hoạt động

2. **Cấu hình Route Tables**:
   - Trong phần **Route tables**, bạn sẽ thấy tất cả các route tables được liên kết với VPC đã chọn
   - **Chọn route table** hiển thị liên kết với **2 subnets**
   
   {{% notice tip %}}
**Quan trọng**: Đây KHÔNG phải là main route table của VPC. Đây là custom route table được tạo bởi CloudFormation đặc biệt cho các private subnets nơi EC2 instances của bạn đang chạy. Gateway endpoint sẽ tự động thêm một route vào bảng này để hướng lưu lượng S3 (prefix list `pl-63a5400a`) đến endpoint.
{{% /notice %}}

![Chọn VPC và Route Table](/images/5-Workshop/5.3-S3-vpc/vpc.png)

**Điều Gì Xảy Ra Phía Sau:**
Khi bạn chọn route table, AWS sẽ tự động thêm một entry như:
```
Destination: pl-63a5400a (S3 prefix list)
Target: vpce-xxxxx (ID gateway endpoint của bạn)
```

---

#### Bước 5: Cấu hình Endpoint Policy

1. **Policy Type**: Giữ nguyên tùy chọn mặc định **Full access**
   
   Điều này cấp quyền truy cập không hạn chế đến tất cả các S3 operations và buckets thông qua endpoint này.

![Cấu hình Endpoint Policy](/images/5-Workshop/5.3-S3-vpc/policy.png)

{{% notice info %}}
**Lưu ý**: Trong phần sau của workshop này, bạn sẽ học cách triển khai **các VPC endpoint policies hạn chế** để giới hạn quyền truy cập vào các S3 buckets hoặc operations cụ thể. Điều này minh họa nguyên tắc least privilege và cho thấy cách endpoint policies cung cấp một lớp bảo mật bổ sung ngoài IAM và bucket policies.
{{% /notice %}}

**Giải Thích Các Tùy Chọn Policy:**
- **Full access**: Cho phép tất cả S3 actions trên tất cả resources
- **Custom**: Cho phép bạn định nghĩa các actions và resources được phép/từ chối cụ thể bằng JSON policy

---

#### Bước 6: Xem lại và Tạo Endpoint

1. **Tags (Tùy chọn)**: Bỏ qua việc thêm tags cho workshop này
   - Trong production, hãy cân nhắc thêm tags như `Environment: Workshop`, `Owner: TenBan`

2. Click **Create endpoint** để cung cấp gateway endpoint

3. **Xác nhận**: Bạn sẽ thấy thông báo thành công cho biết endpoint đã được tạo

4. Click nút **X** hoặc **Close** để quay lại danh sách endpoints

![Tạo Endpoint Thành Công](/images/5-Workshop/5.3-S3-vpc/complete.png)

**Xác minh:**
- Endpoint mới xuất hiện trong danh sách endpoints của bạn với trạng thái **available**
- Kiểm tra route table được liên kết để thấy route S3 prefix list mới

---

#### Hiểu Những Gì Đã Được Tạo

Gateway endpoint của bạn giờ đã hoạt động và đã tự động:
1. **Cập nhật route table** với một entry hướng lưu lượng S3 đến endpoint
2. **Tạo managed prefix list** chứa tất cả các IP ranges của S3 cho us-east-1
3. **Kích hoạt S3 access riêng tư** cho các resources trong các subnets được liên kết

**Bước Tiếp Theo:**
Trong phần tiếp theo, bạn sẽ kiểm tra endpoint này bằng cách truy cập S3 từ một EC2 instance trong VPC của bạn và xác minh rằng lưu lượng đi qua private endpoint thay vì internet.
