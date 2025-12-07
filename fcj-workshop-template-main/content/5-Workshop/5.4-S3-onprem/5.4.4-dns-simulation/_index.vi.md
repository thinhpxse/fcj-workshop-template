---
title : "Cấu Hình Hybrid DNS cho Private Endpoint Resolution"
date : 2025-12-06
weight : 4
chapter : false
pre : " <b> 5.4.4 </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Trong phần này, bạn sẽ cấu hình cơ sở hạ tầng DNS để cho phép phân giải tên liền mạch cho các S3 API calls từ môi trường on-premises của bạn. Điều này mô phỏng cách các mạng doanh nghiệp sử dụng conditional DNS forwarding để phân giải các AWS service endpoints thành địa chỉ IP riêng tư.

**Tại Sao Cấu Hình DNS Rất Quan Trọng:**

AWS PrivateLink endpoints sử dụng elastic network interfaces (ENIs) với địa chỉ IP riêng tư cố định trong mỗi Availability Zone. Mặc dù các IP này vẫn ổn định trong suốt vòng đời của endpoint, AWS rất khuyến khích sử dụng DNS resolution thay vì hardcoding IPs vì:

- **Dynamic AZ Management**: ENIs có thể được thêm vào các AZ mới hoặc bị xóa theo thời gian
- **Automatic Failover**: DNS cho phép chuyển đổi tự động giữa các IP đặc thù của AZ trong các sự cố
- **Service Updates**: AWS có thể cập nhật cơ sở hạ tầng endpoint yêu cầu IP mới
- **Best Practice**: DNS cung cấp tính trừu tượng và linh hoạt cho các kiến trúc doanh nghiệp

**Những Gì Bạn Sẽ Cấu Hình:**

1. **DNS Alias Records**: Ánh xạ S3 service domains đến Interface Endpoint IPs trong Route 53 Private Hosted Zone
2. **Resolver Forwarding Rule**: Hướng các DNS queries on-premises cho S3 đến cloud VPC
3. **End-to-End Testing**: Xác thực DNS resolution và S3 access sử dụng môi trường on-premises mô phỏng

Cấu hình này bắt chước các kiến trúc DNS hybrid thực tế nơi các on-premises DNS servers chuyển tiếp các domains cụ thể một cách có điều kiện đến AWS để phân giải riêng tư.

---

#### Phần 1: Tạo DNS Alias Records cho Interface Endpoint

Bây giờ bạn sẽ tạo DNS records trong Route 53 Private Hosted Zone để ánh xạ tên dịch vụ S3 đến địa chỉ IP riêng tư của Interface Endpoint.

**Hiểu Về Private Hosted Zone:**

CloudFormation stack bạn đã triển khai trước đó đã tạo một Private Hosted Zone cho `s3.us-east-1.amazonaws.com`. Zone này:
- Chỉ được liên kết với VPCs của bạn (không public)
- Ghi đè DNS resolution công cộng cho S3 trong VPCs của bạn
- Cho phép bạn trả về IP riêng tư thay vì IP công cộng cho S3 domains
- Tích hợp với Route 53 Resolver cho hybrid DNS

**Bước 1: Truy Cập Route 53 Hosted Zones**

1. Mở [Route 53 Management Console](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=us-east-1#) (phần Hosted Zones)
2. Bạn sẽ thấy Private Hosted Zone có tên `s3.us-east-1.amazonaws.com`
3. Click vào **tên hosted zone** để xem các DNS records của nó

![Route 53 Private Hosted Zone](/images/5-Workshop/5.4-S3-onprem/hosted-zone.png)

{{% notice info %}}
**Chỉ Báo Private Hosted Zone**: Bạn sẽ thấy "Private" được liệt kê dưới cột Type, và Associated VPCs sẽ hiển thị VPCs nào sử dụng zone này cho DNS resolution. Điều này đảm bảo chỉ các resources trong VPCs của bạn nhận được endpoint IPs riêng tư.
{{% /notice %}}

**Bước 2: Tạo Primary Alias Record**

Bạn sẽ tạo một Alias record trỏ base S3 domain đến Interface Endpoint của bạn.

1. **Khởi tạo việc tạo record**:
   - Click nút **Create record** ở trên cùng

2. **Cấu hình record đầu tiên**:
   
   - **Record name**: Để trống (điều này tạo record cho zone apex: `s3.us-east-1.amazonaws.com`)
   - **Record type**: Chọn **A – IPv4 address** (mặc định)
   
   - **Cấu hình Alias**:
     - Bật công tắc **Alias** lên **ON** (kích hoạt)
     - Điều này cho Route 53 biết để alias đến AWS resource khác
   
   - **Route traffic to**:
     - Chọn **Alias to VPC endpoint** từ dropdown
   
   - **Region**: 
     - Chọn **US East (N. Virginia) [us-east-1]**
   
   - **Choose endpoint**:
     - Dán **Regional VPC Endpoint DNS name** từ text editor của bạn
     - Đây là DNS name bạn đã lưu trong phần 5.4.3
     - Định dạng: `vpce-xxxxxxxxx.s3.us-east-1.vpce.amazonaws.com`

![Create Primary DNS Record](/images/5-Workshop/5.4-S3-onprem/create-record1.png)

![Primary Record Configuration](/images/5-Workshop/5.4-S3-onprem/record1.png)

{{% notice tip %}}
**Alias vs CNAME**: Sử dụng Alias records (thay vì CNAME) cung cấp các lợi ích:
- Hoạt động tại zone apex (bare domain không có subdomain)
- Không tính phí cho Alias queries đến AWS resources
- Hiệu suất tốt hơn với tích hợp dịch vụ AWS
- Hỗ trợ health checking và failover tự động
{{% /notice %}}

**Bước 3: Tạo Wildcard Alias Record**

Bây giờ tạo record thứ hai để xử lý tất cả các subdomain của dịch vụ S3 (ví dụ: `bucket.s3.us-east-1.amazonaws.com`).

1. **Thêm record khác**:
   - Click nút **Add another record** (chưa click Create)

2. **Cấu hình wildcard record**:
   
   - **Record name**: Nhập `*` (dấu sao - điều này tạo wildcard)
   - **Record type**: Giữ **A – IPv4 address**
   
   - **Cấu hình Alias**:
     - Bật **Alias** lên **ON**
   
   - **Route traffic to**:
     - Chọn **Alias to VPC endpoint**
   
   - **Region**: 
     - Chọn **US East (N. Virginia) [us-east-1]**
   
   - **Choose endpoint**:
     - Dán cùng **Regional VPC Endpoint DNS name**

3. **Tạo cả hai records**:
   - Click nút **Create records**
   - Cả hai records sẽ được tạo đồng thời

![Wildcard DNS Record Configuration](/images/5-Workshop/5.4-S3-onprem/record2.png)

**Tại Sao Cần Hai Records?**

Hai DNS records phục vụ các mục đích khác nhau:

1. **Apex Record** (`s3.us-east-1.amazonaws.com`):
   - Xử lý queries đến base S3 regional endpoint
   - Được sử dụng bởi các lệnh như: `aws s3 ls --endpoint-url https://s3.us-east-1.amazonaws.com`

2. **Wildcard Record** (`*.s3.us-east-1.amazonaws.com`):
   - Xử lý queries với tên bucket trong subdomain
   - Khớp với: `bucket.s3.us-east-1.amazonaws.com`, `my-bucket.s3.us-east-1.amazonaws.com`, v.v.
   - Được sử dụng bởi các S3 URLs kiểu path-style

**Bước 4: Xác Minh Việc Tạo Record**

Sau khi tạo, bạn sẽ thấy cả hai records trong hosted zone:

![DNS Records Created Successfully](/images/5-Workshop/5.4-S3-onprem/result.png)

Xác minh:
- Hai A records mới (apex và wildcard)
- Cả hai hiển thị Type: **A - Alias**
- Value trỏ đến VPC endpoint của bạn

{{% notice success %}}
**DNS Records Đã Được Cấu Hình!** Những records này đảm bảo rằng khi các hệ thống query cho S3 domain names, chúng nhận được địa chỉ IP riêng tư của Interface Endpoint ENIs của bạn thay vì S3 IPs công cộng.
{{% /notice %}}

---

#### Phần 2: Tạo Route 53 Resolver Forwarding Rule

Bây giờ bạn sẽ cấu hình conditional DNS forwarding từ on-premises VPC đến cloud VPC. Điều này mô phỏng cách các on-premises DNS servers chuyển tiếp các domains cụ thể đến AWS để phân giải.

**Hiểu Về Resolver Forwarding Rules:**

Route 53 Resolver Forwarding Rules cho phép:
- **Conditional forwarding**: Chỉ các domains cụ thể được chuyển tiếp (ví dụ: `s3.us-east-1.amazonaws.com`)
- **Hybrid DNS**: Các hệ thống on-premises query DNS cục bộ của họ, sau đó chuyển tiếp đến AWS
- **Centralized management**: Rules xác định domains nào phân giải qua AWS
- **Bidirectional resolution**: Có thể chuyển tiếp cả từ AWS đến on-prem và ngược lại

Trong workshop này:
- EC2 on-prem query **Outbound Resolver** trong VPC On-prem
- Outbound Resolver chuyển tiếp S3 queries qua VPN đến **Inbound Resolver** trong VPC Cloud
- Inbound Resolver query Private Hosted Zone
- Response với IP riêng tư quay trở lại on-prem

**Bước 1: Lấy Địa Chỉ IP của Inbound Resolver**

Trước tiên, bạn cần các địa chỉ IP nơi Inbound Resolver đang lắng nghe trong VPC Cloud.

1. **Điều hướng đến Inbound Endpoints**:
   - Từ Route 53 console, click **Resolver** trong thanh bên trái
   - Click **Inbound endpoints**

2. **Truy cập chi tiết endpoint**:
   - Click vào **Endpoint ID** của inbound endpoint
   - CloudFormation đã tạo điều này với tên như `VPCCloudInboundEndpoint`

![Route 53 Inbound Endpoint](/images/5-Workshop/5.4-S3-onprem/route53-1.png)

3. **Sao chép địa chỉ IP**:
   - Trong chi tiết endpoint, cuộn đến phần **IP addresses**
   - Bạn sẽ thấy **2 địa chỉ IP** (một cho mỗi AZ)
   - **Sao chép cả hai IPs** vào text editor của bạn
   - Định dạng: `10.0.x.x` (trong VPC Cloud CIDR)

![Inbound Resolver IP Addresses](/images/5-Workshop/5.4-S3-onprem/route53-2.png)

{{% notice note %}}
**Multi-AZ Resolver**: Inbound Resolver có IPs trong nhiều AZ để có tính sẵn sàng cao. Forwarding rule sẽ sử dụng cả hai, tự động failover nếu một AZ không khả dụng.
{{% /notice %}}

**Bước 2: Tạo Forwarding Rule**

1. **Điều hướng đến Rules**:
   - Trong Route 53 console, click **Resolver** > **Rules** trong thanh bên trái
   - Click nút **Create rule**

![Create Resolver Rule](/images/5-Workshop/5.4-S3-onprem/route53-3.png)

2. **Cấu hình rule cơ bản**:
   
   - **Name**: Nhập `S3-PrivateEndpoint-ForwardingRule`
   - **Description** (tùy chọn): "Forward S3 DNS queries to VPC Cloud for private endpoint resolution"
   - **Rule type**: Chọn **Forward** (không phải System)
   - **Domain name**: Nhập `s3.us-east-1.amazonaws.com`

Domain name này cho rule biết: "Chuyển tiếp bất kỳ DNS queries nào cho domain này và các subdomain của nó."

![Forwarding Rule Basic Configuration](/images/5-Workshop/5.4-S3-onprem/route53-4.png)

**Bước 3: Liên Kết VPC và Outbound Endpoint**

3. **Cấu hình VPC**:
   
   - **VPCs to associate this rule with**:
     - Chọn **VPC On-prem** từ dropdown
     - Điều này áp dụng rule cho các DNS queries bắt nguồn từ on-premises VPC
   
   - **Outbound endpoint**:
     - Chọn **VPCOnpremOutboundEndpoint**
     - Đây là resolver endpoint chuyển tiếp queries từ VPC On-prem

![VPC and Outbound Endpoint Association](/images/5-Workshop/5.4-S3-onprem/route53-5.png)

{{% notice info %}}
**Vai Trò Outbound Endpoint**: Outbound Resolver trong VPC On-prem nhận DNS queries từ các EC2 instances và chuyển tiếp chúng qua VPN tunnel đến các địa chỉ IP đích (Inbound Resolver trong VPC Cloud).
{{% /notice %}}

**Bước 4: Chỉ Định Target IP Addresses**

4. **Thêm target IPs**:
   
   - **Target IP addresses**: Đây là nơi các queries được chuyển tiếp sẽ được gửi đến
   - Click **Add target IP address**
   - **IP address**: Dán **Inbound Resolver IP đầu tiên** từ text editor của bạn
   - Click **Add target IP address** lần nữa
   - **IP address**: Dán **Inbound Resolver IP thứ hai**
   - Cả hai IPs bây giờ nên được liệt kê

5. **Tạo rule**:
   - Click nút **Submit**

![Target IP Configuration](/images/5-Workshop/5.4-S3-onprem/route53-6.png)

**Hiểu Target IPs:**

Đây là Inbound Resolver IPs trong VPC Cloud:
- Chúng lắng nghe cho các DNS queries đến qua VPN
- Chúng query Private Hosted Zone để tìm câu trả lời
- Chúng trả kết quả về qua VPN cho Outbound Resolver

**Bước 5: Xác Minh Việc Tạo Rule**

Sau khi tạo, bạn sẽ thấy forwarding rule mới của bạn:

![Forwarding Rule Created Successfully](/images/5-Workshop/5.4-S3-onprem/route53-7.png)

Kiểm tra rằng:
- **Status** hiển thị **Active**
- **Type** hiển thị **Forward**
- **Domain name** là `s3.us-east-1.amazonaws.com`
- **VPC** hiển thị VPC On-prem

{{% notice success %}}
**DNS Forwarding Đã Được Cấu Hình!** Các queries từ VPC On-prem cho `s3.us-east-1.amazonaws.com` bây giờ chuyển tiếp qua Outbound Resolver, qua VPN tunnel, đến Inbound Resolver, và cuối cùng phân giải qua Private Hosted Zone.
{{% /notice %}}

---

#### Phần 3: Kiểm Tra On-Premises DNS Resolution

Bây giờ xác thực rằng toàn bộ chuỗi DNS resolution đang hoạt động chính xác.

**Bước 1: Kết Nối đến On-Premises Instance**

1. Mở **AWS Systems Manager** > **Session Manager**
2. Click **Start session**
3. Chọn EC2 instance **Test-Interface-Endpoint** (trong VPC On-prem)
4. Click **Start session**

![Connect to Test Instance](/images/5-Workshop/5.4-S3-onprem/test1.png)

**Bước 2: Kiểm Tra DNS Resolution với Lệnh Dig**

Trong Session Manager terminal, chạy:

```bash
dig +short s3.us-east-1.amazonaws.com
```

**Kết Quả Mong Đợi:**

Bạn sẽ thấy **2 địa chỉ IP** được trả về:
```
10.0.1.100
10.0.2.100
```

(Các IP thực tế của bạn sẽ khác nhưng nên nằm trong VPC Cloud CIDR range)

![DNS Resolution Test with Dig](/images/5-Workshop/5.4-S3-onprem/dig.png)

{{% notice note %}}
**Sự Phân Biệt Quan Trọng**: Các địa chỉ IP được trả về là **VPC Interface Endpoint ENI IPs**, KHÔNG phải Inbound Resolver IPs bạn đã cấu hình. Cả hai bộ IPs đều nằm trong VPC Cloud CIDR block, nhưng chúng phục vụ các mục đích khác nhau:
- **Inbound Resolver IPs**: Xử lý DNS queries (không được trả về trong kết quả dig)
- **Interface Endpoint IPs**: Nơi S3 API traffic được gửi đến (được trả về bởi DNS queries)
{{% /notice %}}

**Điều Gì Vừa Xảy Ra:**

Lệnh `dig` đã kích hoạt luồng DNS resolution này:
```
EC2 On-prem → Outbound Resolver (VPC On-prem) 
→ VPN Tunnel → Inbound Resolver (VPC Cloud) 
→ Private Hosted Zone → Alias Record 
→ Interface Endpoint IPs → Response quay lại EC2
```

**Bước 3: Xác Minh IPs Khớp Với Interface Endpoint**

Kiểm tra chéo các IPs được trả về bởi dig với Interface Endpoint thực tế:

1. **Mở VPC Console**:
   - Điều hướng đến **VPC** > **Endpoints**
   - Click vào **S3 Interface Endpoint** của bạn

2. **Kiểm tra tab Subnets**:
   - Click vào tab **Subnets**
   - Bạn sẽ thấy ENI IDs và các IP riêng tư liên kết của chúng
   - **Xác minh** các IPs này khớp với những IPs được trả về bởi lệnh dig

![Verify Endpoint IPs in VPC Console](/images/5-Workshop/5.4-S3-onprem/subnet.png)

Các IPs nên khớp hoàn hảo, xác nhận DNS resolution đang hoạt động chính xác.

{{% notice success %}}
**DNS Resolution Đã Được Xác Thực!** Môi trường on-premises của bạn đang phân giải thành công S3 domain names thành Interface Endpoint IPs riêng tư thông qua kiến trúc DNS hybrid.
{{% /notice %}}

**Bước 4: Kiểm Tra S3 API Access qua DNS**

Bây giờ kiểm tra rằng các ứng dụng có thể sử dụng S3 endpoint URL tiêu chuẩn và tự động kết nối qua private endpoint.

Trong Session Manager terminal, chạy:

```bash
aws s3 ls --endpoint-url https://s3.us-east-1.amazonaws.com
```

**Kết Quả Mong Đợi:**

Bạn sẽ thấy danh sách các S3 buckets của bạn, bao gồm test bucket được tạo trước đó.

![S3 Access via Standard Endpoint URL](/images/5-Workshop/5.4-S3-onprem/endpoint.png)

**Quan Sát Chính:**

Lưu ý bạn đang sử dụng **S3 endpoint URL tiêu chuẩn** (`https://s3.us-east-1.amazonaws.com`), không phải URL đặc thù của VPC endpoint mà bạn đã sử dụng trong phần 5.4.3. Điều này khả thi vì:

1. DNS phân giải `s3.us-east-1.amazonaws.com` thành Interface Endpoint private IPs
2. HTTPS traffic định tuyến qua VPN đến Interface Endpoint
3. Ứng dụng không cần biết về cấu hình private endpoint
4. AWS SDKs và CLI tiêu chuẩn hoạt động mà không cần sửa đổi

Điều này minh họa sức mạnh của DNS-based private endpoint resolution!

{{% notice tip %}}
**Lợi Ích Production**: Trong môi trường doanh nghiệp, các ứng dụng có thể sử dụng AWS service endpoints tiêu chuẩn trong code của chúng. Cơ sở hạ tầng DNS tự động hướng lưu lượng qua private endpoints khi chạy on-premises hoặc trong VPCs, và qua internet khi chạy ở nơi khác (với routing phù hợp).
{{% /notice %}}

**Bước 5: Dọn Dẹp Session**

1. Kết thúc Session Manager session của bạn bằng cách gõ:
   ```bash
   exit
   ```

2. Hoặc đóng tab trình duyệt

![Terminate Session](/images/5-Workshop/5.4-S3-onprem/terminal.png)

---

#### Hiểu Kiến Trúc Hybrid DNS của Bạn

**Luồng Lưu Lượng Hoàn Chỉnh:**

Khi một ứng dụng on-premises truy cập S3, đây là luồng hoàn chỉnh:

**Đường Dẫn DNS Resolution:**
```
┌──────────────────────┐
│ On-Prem Application  │
│ Queries: s3.us-...   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ EC2 Instance         │
│ DNS Resolver Config  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Outbound Resolver    │
│ (VPC On-prem)        │
│ Forwarding Rule:     │
│ s3.us-east-1.*       │
└──────────┬───────────┘
           │
           │ VPN Tunnel
           ▼
┌──────────────────────┐
│ Inbound Resolver     │
│ (VPC Cloud)          │
│ IPs: 10.0.x.x        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Private Hosted Zone  │
│ s3.us-east-1.*.com   │
│ Alias Records        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Returns: 10.0.1.100  │
│          10.0.2.100  │
│ (Endpoint ENI IPs)   │
└──────────────────────┘
```

**Đường Dẫn Dữ Liệu (Sau DNS Resolution):**
```
On-Prem Application → Interface Endpoint Private IP 
→ VPN Tunnel → VPC Cloud → Interface Endpoint ENI 
→ AWS PrivateLink → S3 Service
```

---

#### Những Thành Tựu Chính

**Những Gì Bạn Đã Xây Dựng:**

✅ **DNS Alias Records**: Ánh xạ S3 domains đến Interface Endpoint private IPs  
✅ **Conditional Forwarding**: Cấu hình forwarding rule cho S3 domain từ on-prem đến cloud  
✅ **Hybrid DNS Resolution**: Cho phép DNS resolution liền mạch qua VPN  
✅ **Transparent S3 Access**: Ứng dụng sử dụng S3 endpoints tiêu chuẩn với routing riêng tư  
✅ **High Availability**: Cấu hình DNS và endpoint multi-AZ để chịu lỗi  

**Các Mẫu Kiến Trúc Doanh Nghiệp:**

1. **Conditional DNS Forwarding**: Split-brain DNS với domain-specific forwarding
2. **Private Hosted Zones**: Ghi đè public DNS cho AWS services với IP riêng tư
3. **Alias Records**: Định tuyến DNS hiệu quả đến AWS resources
4. **Resolver Endpoints**: Cầu nối DNS hybrid giữa on-prem và cloud
5. **Multi-AZ Resilience**: Failover tự động cho DNS và endpoints

**Cải Tiến Production:**

Đối với các triển khai thực tế, cân nhắc:
- **DNS caching strategies** để giảm tải resolver query
- **Monitoring CloudWatch metrics** cho số lượng Resolver query và độ trễ
- **Multiple forwarding rules** cho các dịch vụ AWS khác nhau (EC2, RDS, v.v.)
- **DNS failback to public resolution** nếu private endpoints không khả dụng
- **Integration với on-premises DNS servers** (BIND, Active Directory, v.v.)
- **DNS query logging** cho kiểm tra bảo mật và khắc phục sự cố
- **Route 53 Resolver Query Logging** được bật để tuân thủ

Kiến trúc DNS hybrid này cung cấp kết nối riêng tư cấp doanh nghiệp đến các dịch vụ AWS trong khi duy trì code ứng dụng tiêu chuẩn và việc sử dụng AWS SDK!
