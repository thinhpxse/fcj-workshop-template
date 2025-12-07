---
title : "Tạo Interface VPC Endpoint cho Truy Cập S3"
date : 2025-12-06
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Trong phần này, bạn sẽ triển khai một S3 Interface VPC Endpoint trong cloud VPC. Endpoint này cho phép kết nối riêng tư từ mạng on-premises mô phỏng của bạn đến Amazon S3, loại bỏ nhu cầu sử dụng internet gateway hoặc NAT device.

**Tại Sao Sử Dụng Interface Endpoints cho Hybrid Access?**

Interface VPC Endpoints tạo ra các elastic network interfaces (ENIs) với địa chỉ IP riêng tư trong VPC của bạn. Kiến trúc này cung cấp nhiều lợi ích cho các tình huống hybrid cloud:

- **Private IP Addressing**: Các hệ thống on-premises có thể phân giải tên dịch vụ S3 thành các IP riêng tư trong CIDR range của VPC bạn
- **DNS Integration**: Hoạt động liền mạch với Route 53 Resolver cho hybrid DNS resolution
- **Security**: Lưu lượng đi qua các kết nối riêng tư (VPN/Direct Connect) mà không đi qua internet công cộng
- **PrivateLink Technology**: Tận dụng AWS PrivateLink cho truy cập dịch vụ an toàn và có khả năng mở rộng
- **Fine-Grained Access Control**: Áp dụng security groups và endpoint policies để quản lý truy cập chi tiết

{{% notice info %}}
**Interface vs Gateway Endpoints**: Trong khi bạn trước đó đã sử dụng Gateway Endpoint (sửa đổi route tables), Interface Endpoints sử dụng ENIs với IP riêng tư. Điều này làm cho chúng phù hợp cho truy cập on-premises thông qua VPN/Direct Connect, vì chúng có thể được truy cập qua IP routing tiêu chuẩn.
{{% /notice %}}

---

#### Các Bước Tạo Interface Endpoint Chi Tiết

**Bước 1: Điều Hướng đến VPC Endpoints Console**

1. Mở [Amazon VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)
2. Trong menu điều hướng bên trái, click **Endpoints**
3. Click nút **Create endpoint** ở góc trên bên phải

**Bước 2: Cấu Hình Thiết Lập Cơ Bản cho Endpoint**

Trong trang cấu hình Create Endpoint:

1. **Đặt tên endpoint của bạn** (tùy chọn nhưng được khuyến nghị):
   - Nhập tên mô tả: `S3-Interface-Endpoint-HybridAccess`
   - Điều này giúp xác định mục đích của endpoint trong môi trường production

2. **Chọn Service Category**:
   - Chọn **AWS services**
   - Điều này lọc danh sách dịch vụ xuống các AWS-managed endpoints

![Cấu Hình Endpoint Cơ Bản](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint1.png)

{{% notice tip %}}
**Best Practice Đặt Tên**: Sử dụng các tên mô tả cho biết dịch vụ, loại endpoint và mục đích, ví dụ: `S3-Interface-CloudVPC` hoặc `S3-InterfaceEP-Production`.
{{% /notice %}}

**Bước 3: Tìm Kiếm và Chọn S3 Interface Service**

1. **Tìm kiếm dịch vụ S3**:
   - Trong hộp tìm kiếm **Services**, gõ: `S3`
   - Nhấn **Enter** hoặc click biểu tượng tìm kiếm

2. **Xác định dịch vụ chính xác**:
   - Tìm dịch vụ có tên: `com.amazonaws.us-east-1.s3`
   - **Xác minh cột Type hiển thị "Interface"** (không phải Gateway)
   - Click vào nút radio để chọn dịch vụ này

![Chọn S3 Interface Service](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint2.png)

{{% notice warning %}}
**Lựa Chọn Quan Trọng**: AWS cung cấp cả loại endpoint Gateway và Interface cho S3. Đảm bảo bạn chọn loại **Interface**. Gateway endpoints sẽ không hoạt động cho các tình huống truy cập on-premises vì chúng chỉ sửa đổi route tables của VPC.
{{% /notice %}}

**Bước 4: Cấu Hình VPC và Thiết Lập DNS**

1. **Chọn Target VPC**:
   - Từ dropdown **VPC**, chọn **VPC Cloud**
   - **KHÔNG chọn "VPC On-prem"** - endpoint phải được tạo trong cloud VPC

{{% notice warning %}}
**Lựa Chọn VPC Rất Quan Trọng**: Interface Endpoint phải được tạo trong **VPC Cloud**, không phải VPC On-prem. Các hệ thống on-premises sẽ truy cập endpoint này thông qua VPN tunnel mà bạn đã cấu hình trước đó.
{{% /notice %}}

2. **Cấu Hình DNS Settings**:
   - Mở rộng phần **Additional settings**
   - **Bỏ chọn "Enable DNS name"** (vô hiệu hóa nó)
   - Tại sao? Bạn sẽ cấu hình DNS thủ công sử dụng Route 53 Private Hosted Zones để có nhiều kiểm soát hơn

![Cấu Hình VPC và DNS](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint3.png)

{{% notice info %}}
**Lựa Chọn Cấu Hình DNS**: Trong workshop này, bạn đang sử dụng Route 53 Resolver với Private Hosted Zones cho DNS resolution (đã cấu hình trong phần 5.4.1). Điều này cung cấp tính linh hoạt cao hơn và phản ánh các kiến trúc DNS hybrid của doanh nghiệp. Ngoài ra, bật "Enable DNS name" sẽ tự động tạo Route 53 private DNS records, nhưng chúng ta muốn kiểm soát rõ ràng cho mục đích học tập.
{{% /notice %}}

**Bước 5: Chọn Availability Zones và Subnets**

**Cấu Hình High Availability**:
- Chọn **2 subnets** trong các Availability Zones khác nhau:
  - **Availability Zone**: `us-east-1a`
    - Chọn private subnet tương ứng trong VPC Cloud
  - **Availability Zone**: `us-east-1b`
    - Chọn private subnet tương ứng trong VPC Cloud

![Chọn Subnet](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint4.png)

**Tại Sao Sử Dụng Nhiều AZs?**

Triển khai Interface Endpoint ENIs trong nhiều Availability Zones cung cấp:
- **High Availability**: Nếu một AZ gặp sự cố, lưu lượng có thể định tuyến qua AZ khác
- **Fault Tolerance**: Tự động chuyển đổi dự phòng giữa các ENIs trong các AZ khác nhau
- **Geographic Redundancy**: Giảm độ trễ bằng cách có endpoints ở nhiều vị trí
- **Production Best Practice**: AWS khuyến nghị triển khai multi-AZ cho các dịch vụ quan trọng

{{% notice success %}}
**Best Practice**: Luôn triển khai Interface Endpoints trên ít nhất 2 Availability Zones cho các workloads production. Điều này đảm bảo các ứng dụng on-premises của bạn duy trì kết nối S3 ngay cả trong trường hợp AZ bị lỗi.
{{% /notice %}}

**Bước 6: Áp Dụng Security Group**

1. **Chọn Security Group**:
   - Trong dropdown **Security groups**, chọn **SGforS3Endpoint**
   - Security group này được tạo bởi CloudFormation template
   - Nó chứa các inbound rules cho phép lưu lượng HTTPS (port 443) từ on-premises CIDR

![Chọn Security Group](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint5.png)

**Mục Đích Security Group**:

Security group `SGforS3Endpoint` kiểm soát các nguồn nào có thể giao tiếp với Interface Endpoint ENIs:

```
Inbound Rules:
- Protocol: TCP
- Port: 443 (HTTPS)
- Source: VPC On-prem CIDR (ví dụ: 10.1.0.0/16)
- Purpose: Cho phép S3 API calls từ các hệ thống on-premises
```

{{% notice note %}}
**Lớp Bảo Mật**: Security groups hoạt động như tường lửa ảo cho Interface Endpoint của bạn. Mặc dù lưu lượng đến qua VPN, security group cung cấp một lớp kiểm soát bổ sung, tuân theo các nguyên tắc defense-in-depth của AWS.
{{% /notice %}}

**Bước 7: Cấu Hình Endpoint Policy**

1. **Cấu Hình Policy**:
   - Để **Policy** ở **Full access** (mặc định)
   - Điều này cho phép tất cả các hành động S3 thông qua endpoint

2. **Hiểu Về Endpoint Policies**:
   - Endpoint policies kiểm soát những AWS API actions nào có thể được thực hiện thông qua endpoint
   - Chúng hoạt động cùng với IAM policies (cả hai phải cho phép một action)
   - Đối với workshop này, full access đơn giản hóa việc testing

3. **Click Create endpoint**

![Create Endpoint](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint-success.png)

{{% notice tip %}}
**Cân Nhắc Production**: Trong môi trường production, sử dụng các endpoint policies hạn chế theo nguyên tắc least privilege. Ví dụ, giới hạn đối với các S3 buckets cụ thể hoặc chỉ các actions như `s3:GetObject` và `s3:PutObject`.
{{% /notice %}}

**Ví Dụ Restrictive Policy:**
```json
{
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-company-data/*",
        "arn:aws:s3:::my-company-data"
      ]
    }
  ]
}
```

---

#### Xác Minh Việc Tạo Endpoint

**Kiểm Tra Trạng Thái Endpoint:**

1. Việc tạo endpoint thường mất 2-3 phút
2. Làm mới trang Endpoints định kỳ
3. Đợi cho đến khi **Status** thay đổi từ `Pending` sang **Available**
4. Khi đã available, lưu ý **Endpoint ID** (định dạng: `vpce-xxxxxxxxxxxxxxxxx`)

**Kiểm Tra Chi Tiết Endpoint:**

Click vào endpoint mới tạo để xem:
- **DNS names**: Các tên DNS riêng tư được gán cho endpoint
- **Subnets**: Xác minh cả hai AZs được liệt kê với ENI IDs của chúng
- **Network interfaces**: Mỗi subnet có một ENI riêng với một IP riêng tư
- **Security groups**: Xác nhận `SGforS3Endpoint` được gắn vào

**Ghi Lại Địa Chỉ IP Riêng Tư:**

Trong phần **Subnets**, lưu ý các địa chỉ IP riêng tư được gán cho mỗi ENI. Bạn sẽ sử dụng những địa chỉ này trong cấu hình DNS (bước tiếp theo).

---

#### Hiểu Những Gì Bạn Đã Tạo

**Tổng Quan Kiến Trúc:**

Bạn đã triển khai một Interface VPC Endpoint với các thành phần sau:

1. **Hai Elastic Network Interfaces (ENIs)**:
   - Một ENI trong subnet `us-east-1a` với một IP riêng tư (ví dụ: `10.0.1.100`)
   - Một ENI trong subnet `us-east-1b` với một IP riêng tư (ví dụ: `10.0.2.100`)

2. **Bảo Vệ Security Group**:
   - ENIs được bảo vệ bởi security group `SGforS3Endpoint`
   - Chỉ cho phép lưu lượng HTTPS từ on-premises CIDR

3. **Đường Dẫn Kết Nối Riêng Tư**:
   ```
   On-Premises Systems → VPN Tunnel → VPC Cloud 
   → Interface Endpoint ENI → AWS PrivateLink → S3 Service
   ```

**Cách On-Premises Access Hoạt Động:**

Khi một ứng dụng on-premises thực hiện S3 API call:
1. DNS query cho `s3.amazonaws.com` được gửi đến Outbound Resolver
2. Outbound Resolver chuyển tiếp qua VPN đến Inbound Resolver
3. Inbound Resolver query Private Hosted Zone (sẽ được cấu hình tiếp theo)
4. Private Hosted Zone trả về Interface Endpoint private IP
5. Ứng dụng gửi HTTPS request đến private IP thông qua VPN
6. Request đến Interface Endpoint ENI
7. PrivateLink định tuyến request đến dịch vụ S3
8. Response theo đường ngược lại về on-premises

{{% notice success %}}
**Chúc Mừng!** Bạn đã tạo thành công một S3 Interface VPC Endpoint cho hybrid cloud access. Endpoint này cung cấp kết nối an toàn, riêng tư từ môi trường on-premises của bạn đến Amazon S3, loại bỏ việc phơi bày internet và cho phép các kiến trúc hybrid cấp doanh nghiệp.
{{% /notice %}}

---

#### Các Bước Tiếp Theo

Với Interface Endpoint đã được tạo, bạn sẽ tiếp tục:
1. **Cấu hình DNS records** trong Route 53 Private Hosted Zone để phân giải tên DNS S3 thành endpoint IPs
2. **Kiểm tra kết nối** từ EC2 instance on-premises đến S3 thông qua private endpoint
3. **Xác minh luồng lưu lượng** sử dụng VPC Flow Logs và endpoint metrics
4. **Xác thực bảo mật** bằng cách xác nhận tất cả lưu lượng vẫn trong mạng riêng tư của AWS

Interface Endpoint bây giờ đã sẵn sàng để phục vụ như gateway riêng tư của bạn đến S3 cho các hybrid workloads!
