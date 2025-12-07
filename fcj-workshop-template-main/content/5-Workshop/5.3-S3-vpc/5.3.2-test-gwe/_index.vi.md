---
title : "Kiểm Tra Kết Nối Gateway Endpoint"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Bây giờ bạn đã tạo Gateway Endpoint, đã đến lúc xác minh rằng nó hoạt động chính xác. Trong phần này, bạn sẽ tạo một S3 bucket, kết nối với một EC2 instance trong VPC của bạn, và upload một file thử nghiệm lên S3 - tất cả mà không có lưu lượng rời khỏi mạng AWS.

**Mục Tiêu Kiểm Tra:**
- Xác minh kết nối riêng tư đến S3 thông qua Gateway Endpoint
- Xác nhận rằng lưu lượng không đi qua internet công cộng
- Minh họa các S3 operations thành công từ private subnet
- Xác thực cấu hình route table

---

#### Bước 1: Tạo S3 Bucket để Kiểm Tra

Đầu tiên, bạn cần một S3 bucket để kiểm tra việc upload thông qua Gateway Endpoint.

1. **Điều Hướng đến S3 Console:**
   - Mở [S3 Management Console](https://s3.console.aws.amazon.com/s3/buckets)
   - Click nút **Create bucket**

![S3 Create Bucket](/images/5-Workshop/5.3-S3-vpc/create-bucket.png)

2. **Cấu Hình Thiết Lập Bucket:**

   **Tên Bucket:**
   - Nhập tên duy nhất toàn cầu (tên S3 bucket phải là duy nhất trên tất cả các AWS accounts)
   - Định dạng đề xuất: `gateway-endpoint-test-<chu-cai-dau-ten>-<so-ngau-nhien>`
   - Ví dụ: `gateway-endpoint-test-jd-20241207`

![Đặt Tên Bucket](/images/5-Workshop/5.3-S3-vpc/bucket-name.png)

   **AWS Region:**
   - Đảm bảo region là **US East (N. Virginia) us-east-1**
   - Phải khớp với region nơi VPC và Gateway Endpoint của bạn được triển khai

   **Object Ownership:**
   - Để mặc định (**ACLs disabled** - Được khuyến nghị)

   **Block Public Access settings:**
   - Để tất cả các tùy chọn được đánh dấu (mặc định)
   - Điều này đảm bảo bucket vẫn riêng tư

   **Bucket Versioning:**
   - Để mặc định (Disabled)

   **Default Encryption:**
   - Để mặc định (Server-side encryption with Amazon S3 managed keys - SSE-S3)

3. **Tạo Bucket:**
   - Cuộn xuống cuối trang
   - Click **Create bucket**

![Nút Create](/images/5-Workshop/5.3-S3-vpc/create-button.png)

4. **Xác Minh Việc Tạo:**
   - Bạn sẽ thấy thông báo thành công
   - Bucket mới của bạn xuất hiện trong danh sách buckets

![Tạo Bucket Thành Công](/images/5-Workshop/5.3-S3-vpc/bucket-success.png)

{{% notice tip %}}
**Best Practice Bảo Mật**: Lưu ý rằng bucket được tạo với tất cả public access bị chặn theo mặc định. Điều này phù hợp với AWS security best practices. Truy cập chỉ có thể thông qua VPC endpoint và với IAM permissions phù hợp.
{{% /notice %}}

---

#### Bước 2: Kết Nối với EC2 Instance Sử Dụng Session Manager

Bạn sẽ sử dụng **AWS Systems Manager Session Manager** để kết nối an toàn với EC2 instance mà không yêu cầu SSH keys, bastion hosts, hoặc mở các cổng inbound.

**AWS Session Manager là gì?**
Session Manager là một khả năng Systems Manager được quản lý hoàn toàn cung cấp:
- **Truy Cập An Toàn**: Truy cập shell qua trình duyệt mà không cần mở cổng inbound
- **Audit Trail**: Tất cả các sessions được ghi log trong CloudWatch Logs
- **Không Cần SSH Keys**: Không cần quản lý hoặc phân phối SSH keys
- **Đa Nền Tảng**: Hoạt động với cả Linux và Windows instances

**Ngữ Cảnh Kiến Trúc:**
EC2 instance mà bạn sẽ kết nối đang chạy trong **private subnet** trong "VPC Cloud". Nó không có truy cập internet trực tiếp - kết nối đến các dịch vụ AWS (bao gồm Systems Manager) được cung cấp thông qua các SSM VPC Interface Endpoints được tạo trong quá trình triển khai CloudFormation.

1. **Mở Systems Manager Console:**
   - Trong thanh tìm kiếm AWS Management Console, gõ `Systems Manager`
   - Nhấn **Enter** hoặc click vào dịch vụ **Systems Manager**

![Tìm Kiếm Systems Manager](/images/5-Workshop/5.3-S3-vpc/sm.png)

2. **Điều Hướng đến Session Manager:**
   - Trong navigation pane bên trái, tìm phần **Node Management**
   - Click vào **Session Manager**

![Điều Hướng Session Manager](/images/5-Workshop/5.3-S3-vpc/sm1.png)

3. **Bắt Đầu Session Mới:**
   - Click nút **Start session**
   - Từ danh sách các instances có sẵn, chọn instance có tên **Test-Gateway-Endpoint**

{{% notice info %}}
**Chi Tiết Instance**: EC2 instance này được triển khai trong private subnet của "VPC Cloud" và được cấu hình với IAM instance profile cấp các quyền cần thiết cho Session Manager và S3 access. Instance sẽ được sử dụng để xác minh rằng lưu lượng S3 đi qua Gateway Endpoint bạn đã tạo, thay vì public internet.
{{% /notice %}}

![Bắt Đầu Session](/images/5-Workshop/5.3-S3-vpc/start-session.png)

4. **Session Được Thiết Lập:**
   - Session Manager mở một tab trình duyệt mới với shell prompt
   - Bạn sẽ thấy prompt như: `sh-4.2$` hoặc tương tự
   - Điều này cho biết bạn đã kết nối thành công với EC2 instance

![Session Đã Kết Nối](/images/5-Workshop/5.3-S3-vpc/start-session-success.png)

{{% notice tip %}}
**Xác Minh Kết Nối**: Việc Session Manager hoạt động xác nhận rằng các SSM Interface Endpoints đang hoạt động chính xác. Nếu bạn không thể kết nối, điều đó sẽ cho thấy có vấn đề với Interface Endpoints hoặc IAM permissions.
{{% /notice %}}

---

#### Bước 3: Tạo và Upload File Thử Nghiệm lên S3

Bây giờ bạn sẽ tạo một file thử nghiệm trên EC2 instance và upload nó lên S3 thông qua Gateway Endpoint.

1. **Điều Hướng đến Home Directory:**
   
   Thực thi lệnh sau để chuyển sang home directory của ssm-user:
   ```bash
   cd ~
   ```

![Thay Đổi Directory](/images/5-Workshop/5.3-S3-vpc/cli1.png)

   **Tại sao bước này?** Làm việc trong home directory đảm bảo bạn có quyền ghi phù hợp và môi trường làm việc sạch sẽ.

2. **Tạo File Thử Nghiệm:**
   
   Tạo file thử nghiệm 1GB bằng lệnh sau:
   ```bash
   fallocate -l 1G testfile.xyz
   ```

![Tạo File Thử Nghiệm](/images/5-Workshop/5.3-S3-vpc/cli-file.png)

   **Giải Thích Lệnh:**
   - `fallocate`: Phân bổ không gian cho file một cách hiệu quả
   - `-l 1G`: Chỉ định kích thước file là 1 gigabyte
   - `testfile.xyz`: Tên file cho file thử nghiệm của chúng ta
   
   Điều này tạo file 1GB gần như ngay lập tức bằng cách phân bổ không gian đĩa mà không ghi dữ liệu thực tế. Chúng ta sử dụng file lớn để việc upload dễ quan sát hơn và để chứng minh rằng Gateway Endpoint có thể xử lý việc truyền dữ liệu đáng kể.

3. **Upload File lên S3 Bucket:**
   
   Sử dụng AWS CLI để upload file lên S3 bucket của bạn:
   ```bash
   aws s3 cp testfile.xyz s3://YOUR-BUCKET-NAME/
   ```
   
   **Quan trọng**: Thay thế `YOUR-BUCKET-NAME` bằng tên thực tế của bucket bạn đã tạo trong Bước 1.

![Upload File](/images/5-Workshop/5.3-S3-vpc/uploaded.png)

   **Điều Gì Đang Xảy Ra Phía Sau:**
   - EC2 instance thực hiện API call đến S3
   - VPC route table hướng lưu lượng S3 đến Gateway Endpoint (prefix list destination)
   - Lưu lượng đi qua Gateway Endpoint đến S3, ở lại trong mạng AWS
   - **Không sử dụng internet gateway hoặc NAT gateway**

   {{% notice success %}}
**Chỉ Báo Thành Công**: Nếu việc upload hoàn tất thành công, bạn sẽ thấy output hiển thị tiến trình truyền file và hoàn thành. Điều này xác nhận rằng:
- Gateway Endpoint được cấu hình chính xác
- Các route table entries đang hoạt động như mong đợi
- EC2 instance có IAM permissions phù hợp cho S3
- Kết nối riêng tư đến S3 đang hoạt động
{{% /notice %}}

4. **Kết Thúc Session:**
   - Gõ `exit` hoặc đóng tab trình duyệt
   - Session sẽ được kết thúc sạch sẽ

---

#### Bước 4: Xác Minh Object trong S3 Bucket

Cuối cùng, xác nhận rằng file đã được upload thành công bằng cách kiểm tra S3 console.

1. **Quay Lại S3 Console:**
   - Điều hướng trở lại [S3 Management Console](https://s3.console.aws.amazon.com/s3/buckets)

2. **Mở Bucket Của Bạn:**
   - Click vào tên của bucket bạn đã tạo trước đó

3. **Xác Minh Sự Hiện Diện của File:**
   - Bạn sẽ thấy `testfile.xyz` được liệt kê trong tab Objects
   - Kích thước file sẽ hiển thị khoảng 1 GB
   - Lưu ý timestamp upload

![Xác Minh S3 Object](/images/5-Workshop/5.3-S3-vpc/check-s3-bucket.png)

**Checklist Xác Minh:**
- ✓ File `testfile.xyz` xuất hiện trong bucket
- ✓ Kích thước file khớp (1 GB)
- ✓ Timestamp upload là gần đây
- ✓ Không có lỗi trong S3 console

---

#### Hiểu Những Gì Bạn Vừa Hoàn Thành

**Xác Minh Đường Dẫn Mạng:**
Trong bài kiểm tra này, bạn đã thành công:

1. **Tạo Kết Nối Riêng Tư**: Gateway Endpoint cho phép kết nối riêng tư từ VPC của bạn đến S3
2. **Bỏ Qua Public Internet**: Việc upload không đi qua public internet - nó ở hoàn toàn trong AWS's network backbone
3. **Sử Dụng Route-Based Routing**: Route table tự động hướng lưu lượng S3 đến Gateway Endpoint dựa trên S3 prefix list
4. **Chứng Minh Tiết Kiệm Chi Phí**: Bằng cách sử dụng Gateway Endpoint thay vì NAT Gateway, bạn tránh được:
   - Phí theo giờ của NAT Gateway
   - Phí xử lý dữ liệu của NAT Gateway
   - Chi phí truyền dữ liệu internet gateway tiềm năng

**Sơ Đồ Luồng Lưu Lượng:**
```
EC2 Instance → VPC Route Table → Gateway Endpoint → S3 Service
(Private Subnet)  (Routes S3 to vpce-xxx)  (No internet)  (AWS Network)
```

**Sự Khác Biệt Chính so với Truy Cập Qua Internet:**
- **Không có Gateway Endpoint**: EC2 → NAT Gateway → Internet Gateway → Public Internet → S3
- **Có Gateway Endpoint**: EC2 → Gateway Endpoint → S3 (tất cả trong mạng AWS)

{{% notice tip %}}
**Lợi Ích Hiệu Suất**: Gateway Endpoints không chỉ tiết kiệm chi phí mà còn thường cung cấp hiệu suất tốt hơn so với truy cập qua internet do:
- Độ trễ thấp hơn (đường dẫn mạng AWS trực tiếp)
- Băng thông khả dụng cao hơn
- Không có điểm nghẽn NAT Gateway
- Cơ sở hạ tầng AWS backbone chuyên dụng
{{% /notice %}}

---

#### Tóm Tắt Phần

**Xin chúc mừng!** Bạn đã hoàn thành thành công bài tập kiểm tra Gateway Endpoint.

**Những Gì Bạn Đã Học:**
- ✓ Cách tạo và cấu hình S3 buckets với security best practices
- ✓ Sử dụng AWS Systems Manager Session Manager để truy cập instance an toàn
- ✓ Upload objects lên S3 thông qua Gateway VPC Endpoint
- ✓ Xác minh kết nối riêng tư mà không phụ thuộc vào internet gateway
- ✓ Hiểu luồng lưu lượng trong VPC với Gateway Endpoints

**Điểm Chính:**
1. **Gateway Endpoints cung cấp kết nối riêng tư hiệu quả về chi phí** đến S3 và DynamoDB
2. **Không có phí bổ sung** cho việc sử dụng Gateway Endpoints - bạn chỉ trả cho S3 storage và requests
3. **Lưu lượng vẫn riêng tư** - không bao giờ bị lộ ra public internet
4. **Routing tự động** thông qua các route table entries - không cần cấu hình phức tạp
5. **Có thể mở rộng và có tính sẵn sàng cao** - AWS quản lý cơ sở hạ tầng endpoint

**Bước Tiếp Theo:**
Trong các phần sau, bạn sẽ khám phá:
- Tạo Interface VPC Endpoints cho các dịch vụ AWS khác
- Triển khai VPC Endpoint policies để kiểm soát truy cập chi tiết
- Kiểm tra kết nối từ môi trường on-premises (mô phỏng)
- Hiểu sự khác biệt giữa Gateway và Interface Endpoints
