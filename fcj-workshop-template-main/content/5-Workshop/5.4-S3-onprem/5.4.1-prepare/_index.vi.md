---
title : "Chuẩn Bị Môi Trường cho Kết Nối Hybrid"
date : 2025-12-06
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Trong phần này, bạn sẽ chuẩn bị môi trường để mô phỏng kết nối hybrid cloud giữa một datacenter on-premises và AWS. Thiết lập này minh họa cách các tổ chức có thể truy cập an toàn vào các dịch vụ AWS như S3 từ mạng công ty của họ thông qua các kết nối riêng tư.

**Những Gì Bạn Sẽ Cấu Hình:**
1. Triển khai cơ sở hạ tầng DNS bổ sung sử dụng CloudFormation
2. Cấu hình VPN routing để kích hoạt kết nối từ on-premises đến cloud

**Ngữ Cảnh Kiến Trúc:**
Cấu hình này mô phỏng một kiến trúc hybrid thực tế nơi:
- Một datacenter on-premises kết nối với AWS qua Site-to-Site VPN
- Các DNS queries từ on-premises được giải quyết thông qua Route 53
- S3 access từ on-premises đi qua Interface VPC Endpoints
- Tất cả lưu lượng vẫn riêng tư không bị lộ ra internet

---

#### Phần 1: Triển Khai Cơ Sở Hạ Tầng DNS với CloudFormation

Để kích hoạt DNS resolution phù hợp trong môi trường hybrid, bạn sẽ triển khai một CloudFormation stack tạo ra các Route 53 resolver endpoints và private hosted zones.

**Stack Này Tạo Ra Gì:**

CloudFormation template cung cấp ba thành phần DNS quan trọng:

1. **Route 53 Private Hosted Zone**
   - Lưu trữ DNS records (Alias records) cho S3 Interface Endpoint
   - Cho phép các hệ thống on-premises giải quyết tên S3 endpoint thành địa chỉ IP riêng tư
   - Được giới hạn phạm vi cho VPC của bạn để resolution riêng tư

2. **Route 53 Inbound Resolver Endpoint**
   - Được triển khai trong "VPC Cloud"
   - Nhận DNS queries từ các môi trường on-premises
   - Chuyển tiếp queries đến Private Hosted Zone để resolution
   - Tạo elastic network interfaces (ENIs) trong các VPC subnets của bạn

3. **Route 53 Outbound Resolver Endpoint**
   - Được triển khai trong "VPC On-prem"
   - Chuyển tiếp DNS queries cho các S3 domains đến "VPC Cloud"
   - Cho phép conditional DNS forwarding dựa trên tên domain
   - Định tuyến queries thông qua VPN tunnel đến Inbound Resolver

**Sơ Đồ Luồng DNS:**

![Kiến Trúc Route 53 Resolver](/images/5-Workshop/5.4-S3-onprem/route53.png)

**Các Bước Triển Khai:**

1. **Khởi Chạy CloudFormation Stack:**
   
   Click vào link này để mở CloudFormation console với template đã được cấu hình sẵn: [Triển Khai DNS Infrastructure Stack](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://s3.amazonaws.com/reinvent-endpoints-builders-session/R53CF.yaml&stackName=PLOnpremSetup)
   
   Template URL đã được điền sẵn và tên stack được đặt là `PLOnpremSetup`.

![CloudFormation Quick Create](/images/5-Workshop/5.4-S3-onprem/create-stack.png)

2. **Xem Lại và Chấp Nhận Các Giá Trị Mặc Định:**
   
   - Tất cả các tham số được cấu hình sẵn với các giá trị tối ưu
   - Template sẽ tự động phát hiện VPC IDs và cấu hình subnets của bạn
   - Cuộn xuống cuối trang

3. **Xác Nhận và Tạo:**
   
   - Đánh dấu vào ô xác nhận: "I acknowledge that AWS CloudFormation might create IAM resources."
   - Click **Create stack**

![Nút Create Stack](/images/5-Workshop/5.4-S3-onprem/create-stack-button.png)

4. **Theo Dõi Triển Khai (Tùy Chọn):**
   
   - Việc tạo stack thường mất 3-5 phút
   - Bạn có thể theo dõi tiến trình trong tab **Events**
   - **Bạn không cần chờ** - tiếp tục với phần tiếp theo trong khi stack này triển khai

{{% notice info %}}
**Xử Lý Nền**: CloudFormation stack tạo các resolver endpoints có thể mất vài phút. Bước cấu hình tiếp theo (cập nhật routing table) có thể được hoàn thành song song trong khi CloudFormation chạy.
{{% /notice %}}

**Những Gì Được Tạo:**
- 2 Resolver Endpoint ENIs (Inbound trong VPC Cloud)
- 2 Resolver Endpoint ENIs (Outbound trong VPC On-prem)
- 1 Private Hosted Zone cho DNS resolution của S3 endpoint
- Resolver rules cho conditional DNS forwarding

---

#### Phần 2: Cấu Hình VPN Routing cho Kết Nối On-Premises

Bây giờ bạn sẽ cấu hình routing table trong "VPC On-prem" để hướng lưu lượng đến cho môi trường cloud thông qua VPN tunnel.

**Hiểu Về Thiết Lập VPN:**

Workshop này sử dụng giải pháp VPN dựa trên phần mềm chạy trên EC2 instance để mô phỏng kết nối giữa môi trường on-premises mô phỏng của bạn và AWS:

- **Phần Mềm VPN**: strongSwan (VPN dựa trên IPsec mã nguồn mở)
- **VPN Gateway Instance**: EC2 instance hoạt động như customer gateway
- **Loại Kết Nối**: Site-to-Site VPN tunnel
- **Mục Đích**: Mô phỏng VPN appliance của một datacenter thực tế

{{% notice tip %}}
**Lưu Ý Production**: Trong các triển khai thực tế, bạn sẽ sử dụng:
- VPN appliances vật lý hoặc ảo trong datacenter của bạn
- AWS Site-to-Site VPN với các kết nối dự phòng
- AWS Transit Gateway cho kết nối tập trung
- Nhiều VPN tunnels để có tính sẵn sàng cao
{{% /notice %}}

**Các Bước Cấu Hình:**

**Bước 1: Xác Định VPN Gateway Instance**

1. Mở [Amazon EC2 Console](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#Instances:)

2. Tìm và chọn EC2 instance có tên **infra-vpngw-test**
   - Instance này đang chạy phần mềm strongSwan VPN
   - Nó được cấu hình để terminate VPN tunnel từ "VPC Cloud"

3. Trong tab **Details** (ngăn dưới), tìm **Instance ID**
   - Định dạng: `i-0123456789abcdef0`
   - **Sao chép Instance ID này** vào clipboard hoặc text editor của bạn
   - Bạn sẽ cần ID này trong các bước tiếp theo

![ID của VPN Gateway Instance](/images/5-Workshop/5.4-S3-onprem/ec2-onprem-id.png)

{{% notice note %}}
**Vai Trò Instance**: EC2 instance này có IP forwarding được kích hoạt và được cấu hình với các IPsec tunnels. Nó hoạt động như gateway giữa mạng on-premises mô phỏng và AWS Transit Gateway.
{{% /notice %}}

**Bước 2: Điều Hướng đến VPC Route Tables**

1. Sử dụng **search box** ở đầu AWS Console
2. Gõ `VPC` và nhấn Enter
3. Từ menu bên trái VPC Dashboard, click **Route Tables**

**Bước 3: Cập Nhật Route Table On-Premises**

1. **Tìm route table chính xác:**
   - Lọc hoặc tìm kiếm route table có tên **RT Private On-prem**
   - Đây là route table được liên kết với các private subnets trong "VPC On-prem"
   - Click vào route table để chọn nó

2. **Truy cập chỉnh sửa route:**
   - Click vào tab **Routes** trong ngăn chi tiết dưới
   - Click nút **Edit routes**

![Chọn Route Table](/images/5-Workshop/5.4-S3-onprem/rt.png)

3. **Thêm route entry mới:**
   - Click nút **Add route**
   
   Cấu hình route mới:
   - **Destination**: Nhập Cloud VPC CIDR block của bạn
     - Định dạng: `10.0.0.0/16` (hoặc CIDR nào Cloud VPC của bạn sử dụng)
     - Điều này cho route table biết tài nguyên cloud nằm ở đâu
   
   - **Target**: Chọn **Instance**, sau đó chọn instance **infra-vpngw-test**
     - Dán Instance ID bạn đã sao chép trước đó
     - Hoặc bắt đầu gõ tên instance và chọn từ dropdown

![Cấu Hình Add Route](/images/5-Workshop/5.4-S3-onprem/add-route.png)

4. **Lưu cấu hình:**
   - Click **Save changes**
   - Route table được cập nhật ngay lập tức

**Hiểu Những Gì Bạn Đã Cấu Hình:**

Route entry này cho các instances trong "VPC On-prem" biết rằng:
```
Lưu lượng đến cho Cloud VPC CIDR → Gửi đến VPN Gateway Instance
```

**Luồng Lưu Lượng:**
```
On-Prem EC2 Instance → Route Table Lookup → VPN Gateway EC2 
→ IPsec Tunnel → Transit Gateway → VPC Cloud
```

{{% notice success %}}
**Routing Đã Được Cấu Hình!** Bây giờ bất kỳ lưu lượng nào từ "VPC On-prem" đến cho các resources trong "VPC Cloud" sẽ được định tuyến qua VPN tunnel. Điều này mô phỏng cách mạng công ty của bạn sẽ định tuyến lưu lượng đến AWS qua kết nối Site-to-Site VPN.
{{% /notice %}}

---

#### Các Bước Xác Minh

**Xác Minh CloudFormation Stack:**
1. Quay lại [CloudFormation Console](https://us-east-1.console.aws.amazon.com/cloudformation/)
2. Kiểm tra rằng stack **PLOnpremSetup** hiển thị trạng thái `CREATE_COMPLETE`
3. Click vào stack và xem tab **Resources** để thấy các thành phần đã tạo

**Xác Minh Route Table:**
1. Quay lại route table **RT Private On-prem**
2. Xác nhận route mới xuất hiện với:
   - Destination: Cloud VPC CIDR
   - Target: VPN Gateway instance ID
   - Status: Active

**Tiếp Theo Là Gì:**
Với cơ sở hạ tầng DNS và routing đã có, bạn đã sẵn sàng để:
- Tạo Interface VPC Endpoint cho S3
- Kiểm tra S3 access từ môi trường on-premises mô phỏng
- Xác minh DNS resolution thông qua Route 53 Resolver
- Xác nhận tất cả lưu lượng đi qua các kết nối riêng tư

---

#### Tóm Tắt Kiến Trúc

Bạn đã thiết lập nền tảng kết nối hybrid:

**Đường Dẫn DNS Resolution:**
```
On-Prem Application → Outbound Resolver Endpoint → VPN Tunnel 
→ Inbound Resolver Endpoint → Private Hosted Zone → S3 Endpoint IP
```

**Đường Dẫn Dữ Liệu:**
```
On-Prem Application → VPN Gateway → IPsec Tunnel → Transit Gateway 
→ VPC Cloud → Interface Endpoint → S3
```

Kiến trúc này minh họa các AWS best practices cho:
- Kết nối riêng tư đến các dịch vụ AWS từ on-premises
- DNS resolution trong môi trường hybrid
- Truyền dữ liệu an toàn không qua internet
- Kết nối VPN có thể mở rộng thông qua Transit Gateway



