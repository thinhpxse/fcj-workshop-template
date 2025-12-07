---
title : "Kiểm Tra Kết Nối Interface Endpoint từ On-Premises"
date : 2025-12-06
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Trong phần này, bạn sẽ xác minh rằng Interface VPC Endpoint đang hoạt động chính xác bằng cách kiểm tra kết nối S3 từ môi trường on-premises mô phỏng. Điều này minh họa cách các ứng dụng doanh nghiệp chạy trong các datacenter công ty có thể truy cập an toàn vào các dịch vụ AWS thông qua kết nối riêng tư.

**Mục Tiêu Kiểm Tra:**

1. Lấy tên DNS của Interface Endpoint cho các S3 API calls
2. Kết nối đến EC2 instance mô phỏng máy chủ on-premises
3. Tải dữ liệu lên S3 sử dụng kết nối private endpoint
4. Xác minh việc truyền dữ liệu thành công qua kiến trúc hybrid

**Tại Sao Bài Kiểm Tra Này Quan Trọng:**

Xác thực này xác nhận rằng cấu hình mạng hybrid của bạn đang hoạt động chính xác:
- DNS resolution từ on-premises đến endpoint private IPs
- VPN routing hướng lưu lượng đến cloud VPC
- Interface Endpoint ENIs nhận và xử lý các S3 API requests
- Security groups cho phép luồng lưu lượng phù hợp
- Kết nối riêng tư end-to-end không phơi bày internet

---

#### Phần 1: Lấy Thông Tin DNS của Interface Endpoint

Trước khi kiểm tra kết nối, bạn cần lấy tên DNS đặc thù của endpoint mà các hệ thống on-premises sẽ sử dụng để truy cập S3.

**Bước 1: Truy Cập VPC Endpoints Console**

1. Mở [Amazon VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)
2. Trong ngăn điều hướng bên trái, click **Endpoints**
3. Bạn sẽ thấy Interface Endpoint vừa tạo gần đây

**Bước 2: Tìm Endpoint DNS Names**

1. **Tìm endpoint của bạn**:
   - Tìm kiếm endpoint bạn đã tạo trong phần trước
   - Nó nên có tên như `S3-Interface-Endpoint-HybridAccess` hoặc tương tự
   - Click vào **Endpoint ID** (định dạng: `vpce-xxxxxxxxx`) để xem chi tiết

2. **Truy cập cấu hình DNS**:
   - Trong trang chi tiết endpoint, cuộn đến tab **Details**
   - Tìm phần **DNS names**

3. **Xác Định Regional DNS Name**:
   - Bạn sẽ thấy nhiều DNS names được liệt kê
   - **Sao chép DNS name đầu tiên** (regional endpoint DNS name)
   - Định dạng: `vpce-xxxxxxxxx.s3.us-east-1.vpce.amazonaws.com`
   - Lưu vào text editor - bạn sẽ cần nó ngay sau đây

![Endpoint DNS Names](/images/5-Workshop/5.4-S3-onprem/dns.png)

{{% notice info %}}
**Các Loại DNS Name**: Interface Endpoints cung cấp nhiều DNS names:
- **Regional DNS name**: Hoạt động trên tất cả các AZ (được khuyến nghị cho sử dụng chung)
- **Zonal DNS names**: Dành riêng cho mỗi AZ nơi bạn triển khai ENIs
- **Private DNS name**: Chỉ hoạt động nếu bạn bật private DNS (mà chúng ta đã không bật)

Cho bài test này, sử dụng **regional DNS name** vì nó cung cấp tự động failover giữa các AZ.
{{% /notice %}}

**Hiểu Cấu Trúc DNS Name:**

Regional DNS name tuân theo mẫu này:
```
vpce-<endpoint-id>.s3.<region>.vpce.amazonaws.com
```

DNS name này phân giải thành các địa chỉ IP riêng tư của các Interface Endpoint ENIs được triển khai trong VPC subnets của bạn.

---

#### Phần 2: Kết Nối đến Máy Chủ On-Premises Mô Phỏng

Bây giờ bạn sẽ kết nối đến EC2 instance mô phỏng máy chủ on-premises trong datacenter công ty của bạn.

**Bước 1: Mở AWS Systems Manager Session Manager**

1. **Điều hướng đến Systems Manager**:
   - Trong thanh tìm kiếm AWS Console ở trên cùng, gõ `Session Manager`
   - Chọn **Session Manager** từ kết quả (dưới Systems Manager)

2. **Truy cập Session Manager**:
   - Bạn sẽ được đưa đến trang khởi động Session Manager
   - Dịch vụ này cung cấp quyền truy cập shell an toàn mà không cần SSH keys hoặc bastion hosts

{{% notice tip %}}
**Ưu Điểm Session Manager**: Session Manager cung cấp quyền truy cập shell an toàn dựa trên trình duyệt đến các EC2 instances mà không cần:
- Mở cổng SSH inbound (bảo mật được cải thiện)
- Quản lý SSH keys hoặc credentials
- Triển khai bastion hosts
- Cấu hình security group SSH rules

Hoàn hảo cho các môi trường doanh nghiệp có yêu cầu bảo mật nghiêm ngặt.
{{% /notice %}}

**Bước 2: Khởi Động Interactive Session**

1. **Bắt đầu kết nối**:
   - Click nút **Start session**
   - Bạn sẽ thấy danh sách các EC2 instances có sẵn

2. **Chọn instance on-premises**:
   - Tìm instance có tên **Test-Interface-Endpoint**
   - EC2 instance này chạy trong subnet "VPC On-prem"
   - Nó mô phỏng một máy chủ trong datacenter công ty của bạn
   - Click vào nút radio bên cạnh instance này

3. **Bắt đầu session**:
   - Click nút **Start session** ở dưới cùng
   - Session Manager sẽ mở một tab trình duyệt mới với shell prompt
   - Bạn sẽ thấy: `sh-4.2$` hoặc prompt tương tự

![Start Session Manager Session](/images/5-Workshop/5.4-S3-onprem/start-session.png)

{{% notice note %}}
**Ngữ Cảnh Instance**: EC2 instance Test-Interface-Endpoint được cấu hình để:
- Chạy trong private subnet "VPC On-prem"
- Định tuyến lưu lượng qua VPN Gateway bạn đã cấu hình
- Sử dụng Route 53 Outbound Resolver cho DNS queries
- Mô phỏng máy chủ ứng dụng on-premises truy cập các dịch vụ AWS
{{% /notice %}}

---

#### Phần 3: Chuẩn Bị Dữ Liệu Test để Upload

Bây giờ bạn sẽ tạo một test file để upload lên S3, minh họa việc truyền dữ liệu qua private endpoint.

**Bước 1: Điều Hướng đến User Home Directory**

Trong Session Manager terminal, thực thi:

```bash
cd ~
```

Lệnh này chuyển đến home directory của `ssm-user` nơi bạn có quyền ghi.

**Bước 2: Tạo Test File**

Tạo một test file 1GB để mô phỏng việc truyền dữ liệu thực tế:

```bash
fallocate -l 1G onprem-data.dat
```

**Giải Thích Lệnh:**
- `fallocate`: Tạo file với kích thước được chỉ định một cách hiệu quả
- `-l 1G`: Phân bổ 1 gigabyte dung lượng
- `onprem-data.dat`: Tên file đầu ra

Lệnh này tạo một sparse file ngay lập tức mà không ghi dữ liệu thực tế (hoàn hảo cho việc testing).

![Create Test File](/images/5-Workshop/5.4-S3-onprem/cli1.png)

**Bước 3: Xác Minh Việc Tạo File**

Xác nhận file đã được tạo:

```bash
ls -lh onprem-data.dat
```

Bạn sẽ thấy output hiển thị file 1.0G.

{{% notice tip %}}
**Lựa Chọn Kích Thước File**: Sử dụng file 1GB cung cấp:
- Thời gian truyền có thể đo lường để quan sát hiệu suất mạng
- Kích thước đủ lớn để xác minh throughput qua VPN và endpoint
- Đại diện thực tế cho việc truyền dữ liệu doanh nghiệp
- Đủ nhanh cho mục đích workshop (upload đầy đủ trong vài giây qua mạng AWS)
{{% /notice %}}

---

#### Phần 4: Upload Dữ Liệu Qua Interface Endpoint

Bây giờ đến bài kiểm tra quan trọng: upload dữ liệu lên S3 sử dụng Interface Endpoint bạn đã tạo.

**Bước 1: Xây Dựng Endpoint URL**

Đối với Interface Endpoints, bạn phải chỉ định custom endpoint URL. Định dạng là:

```
https://bucket.<regional-dns-name>
```

**Xây dựng URL của bạn:**
1. Bắt đầu với: `https://bucket.`
2. Thêm vào regional DNS name bạn đã sao chép trước đó
3. Kết quả ví dụ: `https://bucket.vpce-0a1b2c3d4e5f.s3.us-east-1.vpce.amazonaws.com`

{{% notice warning %}}
**Định Dạng DNS Name**: Khi sao chép regional DNS name từ VPC console:
- KHÔNG bao gồm dấu sao đầu tiên (`*.`) nếu được hiển thị
- Chỉ sử dụng phần: `vpce-xxxxxxx.s3.us-east-1.vpce.amazonaws.com`
- Tiền tố `bucket.` được thêm vào trong endpoint URL
{{% /notice %}}

**Bước 2: Xác Định S3 Bucket của Bạn**

Bạn cần tên của S3 bucket đã tạo trước đó trong phần 5.3 (kiểm tra Gateway Endpoint):
- Định dạng: Có thể là `my-endpoint-test-bucket-123456`
- Nếu bạn không nhớ, kiểm tra S3 console trong tab trình duyệt khác

**Bước 3: Thực Thi Lệnh S3 Upload**

Trong Session Manager terminal, chạy:

```bash
aws s3 cp --endpoint-url https://bucket.<Regional-DNS-Name> onprem-data.dat s3://<your-bucket-name>
```

**Thay thế các placeholder:**
- `<Regional-DNS-Name>`: DNS name endpoint thực tế của bạn (không có dấu sao)
- `<your-bucket-name>`: Tên S3 bucket thực tế của bạn

**Ví dụ với giá trị thực:**
```bash
aws s3 cp --endpoint-url https://bucket.vpce-0a1b2c3d4e.s3.us-east-1.vpce.amazonaws.com onprem-data.dat s3://my-endpoint-test-bucket-123456
```

![Upload File Through Endpoint](/images/5-Workshop/5.4-S3-onprem/cli2.png)

**Hiểu Lệnh:**

- `aws s3 cp`: Lệnh AWS CLI để sao chép file đến/từ S3
- `--endpoint-url`: **Tham số quan trọng** - hướng S3 API call đến Interface Endpoint thay vì public S3 endpoint
- `onprem-data.dat`: File nguồn (file cục bộ trên instance)
- `s3://<bucket-name>`: S3 bucket đích

{{% notice info %}}
**Tại Sao --endpoint-url Bắt Buộc**: Theo mặc định, AWS CLI sử dụng public S3 endpoint (`s3.amazonaws.com`). Tham số `--endpoint-url` ghi đè điều này để sử dụng DNS name riêng tư của Interface Endpoint, đảm bảo lưu lượng định tuyến qua VPN và private endpoint thay vì internet.
{{% /notice %}}

**Bước 4: Theo Dõi Tiến Trình Upload**

Bạn sẽ thấy output tương tự như:

```
upload: ./onprem-data.dat to s3://my-bucket/onprem-data.dat
Completed 1.0 GiB/1.0 GiB (50.0 MiB/s)
```

Upload sẽ hoàn thành trong vài giây nhờ mạng backbone tốc độ cao của AWS.

**Điều Gì Vừa Xảy Ra:**

Đằng sau hậu trường, dữ liệu của bạn đã đi qua đường dẫn này:
```
EC2 On-Prem Instance → Route Table Lookup → VPN Gateway EC2 
→ IPsec VPN Tunnel → Transit Gateway → VPC Cloud 
→ Interface Endpoint ENI (Private IP) → AWS PrivateLink → S3 Service
```

Tất cả lưu lượng vẫn trên mạng riêng tư AWS - **không phơi bày internet!**

---

#### Phần 5: Xác Minh Upload Thành Công trong S3 Console

Cuối cùng, xác nhận dữ liệu đã đến thành công trong S3 bucket của bạn.

**Bước 1: Điều Hướng đến S3 Console**

1. Mở [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home?region=us-east-1)
2. Bạn sẽ thấy danh sách tất cả các S3 buckets của bạn

**Bước 2: Truy Cập Bucket của Bạn**

1. **Tìm bucket của bạn**:
   - Tìm bucket bạn đã sử dụng trong lệnh upload
   - Click vào **tên bucket** để xem nội dung của nó

2. **Xem objects**:
   - Bây giờ bạn sẽ thấy file `onprem-data.dat` được liệt kê
   - Kiểm tra cột **Size** - nó sẽ hiển thị 1.0 GB
   - Lưu ý timestamp **Last modified** - nên là gần đây

![Verify Object in S3 Bucket](/images/5-Workshop/5.4-S3-onprem/check-bucket.png)

**Bước 3: Kiểm Tra Chi Tiết Object (Tùy Chọn)**

Click vào tên object để xem các thuộc tính chi tiết:
- **Storage class**: Có khả năng là Standard
- **Server-side encryption**: Có thể hiển thị mã hóa mặc định
- **Metadata**: Bất kỳ metadata tùy chỉnh nào được thêm
- **Tags**: Bất kỳ tags nào được áp dụng trong quá trình upload

{{% notice success %}}
**Test Thành Công!** Môi trường on-premises mô phỏng của bạn đã upload thành công dữ liệu lên S3 thông qua:
- Route 53 Outbound Resolver (DNS query từ on-prem)
- VPN Tunnel (kết nối mạng)
- Route 53 Inbound Resolver (DNS resolution thành private IP)
- Interface VPC Endpoint (điểm truy cập S3 riêng tư)
- AWS PrivateLink (kết nối dịch vụ an toàn)

Tất cả lưu lượng vẫn riêng tư mà không đi qua internet công cộng!
{{% /notice %}}

---

#### Hiểu Kết Quả Kiểm Tra của Bạn

**Xác Thực Kết Nối:**

Bài test thành công này xác nhận:

1. **DNS Resolution Hoạt Động**:
   - Instance on-prem query Outbound Resolver
   - Query được chuyển tiếp qua VPN đến Inbound Resolver
   - Endpoint DNS name được phân giải thành Interface Endpoint private IP

2. **Network Routing Hoạt Động**:
   - VPN tunnel mang lưu lượng giữa on-prem và cloud VPCs
   - Route tables định hướng lưu lượng chính xác
   - Transit Gateway (nếu sử dụng) routing giữa các VPCs

3. **Cấu Hình Security Chính Xác**:
   - Security groups cho phép lưu lượng HTTPS đến endpoint ENIs
   - Endpoint policy cho phép các hoạt động S3
   - IAM permissions trên EC2 instance cho phép truy cập S3

4. **Interface Endpoint Hoạt Động**:
   - ENIs nhận requests trên private IPs
   - PrivateLink routing requests đến dịch vụ S3
   - Responses trả về qua đường dẫn riêng tư

**Sơ Đồ Luồng Lưu Lượng:**

```
┌─────────────────┐   DNS Query      ┌──────────────────┐
│  On-Prem EC2    │─────────────────>│ Outbound Resolver│
│  Instance       │                  │  (VPC On-prem)   │
└────────┬────────┘                  └────────┬─────────┘
         │                                    │
         │ Data Upload (HTTPS)                │ DNS Forward
         │                                    │
         ▼                                    ▼
┌─────────────────┐   VPN Tunnel    ┌──────────────────┐
│  VPN Gateway    │◄────────────────►│ Inbound Resolver │
│  EC2 Instance   │                  │  (VPC Cloud)     │
└────────┬────────┘                  └────────┬─────────┘
         │                                    │
         │                                    │ DNS Response
         ▼                                    │ (Private IP)
┌─────────────────┐                          │
│ Transit Gateway │                          │
└────────┬────────┘                          │
         │                                    │
         ▼                                    ▼
┌─────────────────┐                 ┌──────────────────┐
│  VPC Cloud      │                 │ Private Hosted   │
│  Route Table    │                 │ Zone (Route 53)  │
└────────┬────────┘                 └──────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  Interface Endpoint ENI         │
│  (Private IP: 10.0.x.x)         │
└────────┬────────────────────────┘
         │
         │ AWS PrivateLink
         ▼
┌─────────────────────────────────┐
│  Amazon S3 Service              │
│  (Backend in AWS Network)       │
└─────────────────────────────────┘
```

---

#### Dọn Dẹp cho Phần Tiếp Theo (Tùy Chọn)

Nếu bạn muốn kiểm tra lại hoặc chuẩn bị cho phần workshop tiếp theo:

**Trong Session Manager terminal:**

```bash
# Xóa test file cục bộ để giải phóng không gian
rm onprem-data.dat
```

**Trong S3 Console (tùy chọn):**
- Bạn có thể để lại object trong S3 hoặc xóa nó
- Object sẽ được dọn dẹp trong phần cleanup cuối cùng của workshop

{{% notice note %}}
**Để Cơ Sở Hạ Tầng Chạy**: KHÔNG xóa Interface Endpoint, cấu hình VPN, hoặc Route 53 Resolvers ngay bây giờ. Chúng sẽ được sử dụng trong các phần tiếp theo của workshop và được dọn dẹp ở cuối.
{{% /notice %}}

---

#### Những Điểm Chính Cần Nhớ

**Những Gì Bạn Đã Hoàn Thành:**

✅ **Lấy được Interface Endpoint DNS names** cho cấu hình truy cập API  
✅ **Kết nối đến máy chủ on-premises mô phỏng** sử dụng Session Manager  
✅ **Tạo dữ liệu test** đại diện cho các file công ty  
✅ **Upload dữ liệu lên S3** thông qua private Interface Endpoint  
✅ **Xác minh truyền dữ liệu thành công** trong S3 console  
✅ **Xác thực kết nối hybrid end-to-end** qua nhiều dịch vụ AWS  

**Các Mẫu Kiến Trúc Doanh Nghiệp Được Minh Họa:**

1. **Hybrid Cloud Connectivity**: Kết nối dựa trên VPN giữa on-prem và AWS
2. **Private Service Access**: Sử dụng VPC Endpoints để tránh routing internet
3. **DNS Resolution**: Kiến trúc DNS hybrid với Route 53 Resolver
4. **Secure Remote Access**: Session Manager cho kết nối không cần bastion
5. **Defense in Depth**: Nhiều lớp (VPN, security groups, endpoint policies)

**Cân Nhắc Production:**

Đối với các triển khai thực tế, nâng cao kiến trúc này với:
- **AWS Direct Connect** thay vì VPN cho kết nối mạng chuyên dụng
- **Multiple VPN tunnels** cho tính dự phòng và độ sẵn sàng cao hơn
- **VPC Flow Logs** để giám sát và kiểm tra các mẫu lưu lượng
- **CloudWatch metrics** cho giám sát hiệu suất endpoint
- **Restrictive endpoint policies** giới hạn truy cập đến các buckets cụ thể
- **Private DNS enablement** cho DNS resolution tự động
- **Multi-region endpoints** cho các tình huống disaster recovery

Bài test này xác thực rằng kiến trúc hybrid của bạn đã sẵn sàng cho production để truy cập S3 riêng tư từ các hệ thống on-premises!




