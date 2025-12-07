---
title : "Triển Khai Kiểm Soát Truy Cập Chi Tiết với VPC Endpoint Policies"
date : 2025-12-06
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

Trong phần này, bạn sẽ triển khai các best practices bảo mật bằng cách áp dụng các VPC endpoint policies hạn chế để kiểm soát S3 buckets nào có thể được truy cập thông qua Gateway VPC Endpoint của bạn. Điều này minh họa nguyên tắc least privilege trong kiến trúc cloud.

**Hiểu Về VPC Endpoint Policies:**

VPC endpoint policies là các IAM resource policies cung cấp kiểm soát truy cập chi tiết cho lưu lượng đi qua VPC endpoints. Chúng hoạt động như một lớp bảo mật bổ sung:

- **Default Behavior**: Khi bạn tạo endpoint mà không có custom policy, AWS áp dụng default policy cho phép truy cập đầy đủ đến tất cả resources của dịch vụ đích
- **Policy Type**: Các IAM resource policies dựa trên JSON được gắn trực tiếp vào endpoint
- **Enforcement Point**: Được đánh giá khi lưu lượng đi qua endpoint (không phải phía AWS service)
- **Combined Evaluation**: Hoạt động kết hợp với IAM user/role policies và S3 bucket policies (tất cả phải cho phép một action)
- **Scope**: Có thể hạn chế truy cập theo resource (buckets cụ thể), principal (IAM identities cụ thể), hoặc action (S3 operations cụ thể)

**Tại Sao Sử Dụng Endpoint Policies?**

Endpoint policies rất quan trọng cho các kiến trúc bảo mật doanh nghiệp:

1. **Defense in Depth**: Lớp bảo mật bổ sung ngoài IAM và resource policies
2. **Network-Level Control**: Hạn chế những gì có thể được truy cập qua các network paths cụ thể
3. **Compliance Requirements**: Đáp ứng yêu cầu quy định về kiểm soát truy cập dữ liệu
4. **Prevent Data Exfiltration**: Giới hạn buckets nào có thể được truy cập từ VPC resources
5. **Cost Optimization**: Hạn chế truy cập chỉ đến các resources được ủy quyền, ngăn chặn lạm dụng

**Kịch Bản Workshop:**

Bạn sẽ tạo một endpoint policy hạn chế:
- Cho phép truy cập chỉ đến một S3 bucket cụ thể
- Chặn truy cập đến tất cả các S3 buckets khác
- Minh họa cách endpoint policies thực thi kiểm soát truy cập

![Kiến Trúc Endpoint Policy](/images/5-Workshop/5.5-Policy/s3-bucket-policy.png)

---

#### Phần 1: Thiết Lập Kết Nối Cơ Bản

Trước khi áp dụng policy hạn chế, xác minh quyền truy cập hiện tại để minh họa so sánh trước/sau.

**Bước 1: Kết Nối đến Test Instance**

1. Mở **AWS Systems Manager** > **Session Manager**
2. Click **Start session**
3. Chọn EC2 instance có tên **Test-Gateway-Endpoint**
   - Instance này nằm trong VPC với Gateway Endpoint
   - Nó định tuyến lưu lượng S3 qua Gateway Endpoint thông qua cấu hình route table
4. Click **Start session**

**Bước 2: Xác Minh Truy Cập đến S3 Bucket Gốc**

Trong Session Manager terminal, liệt kê nội dung của bucket bạn đã tạo trong phần 5.3:

```bash
aws s3 ls s3://<your-bucket-name>
```

Thay thế `<your-bucket-name>` bằng tên bucket thực tế của bạn (ví dụ: `my-endpoint-test-bucket-123456`).

![Xác Minh Truy Cập Bucket Gốc](/images/5-Workshop/5.5-Policy/test1.png)

**Kết Quả Mong Đợi:**

Bạn sẽ thấy hai test files 1GB đã upload trong các phần trước:
```
2024-12-07 10:30:15 1073741824 testfile.txt
2024-12-07 11:45:22 1073741824 onprem-data.dat
```

{{% notice info %}}
**Quyền Truy Cập Hiện Tại**: Điều này hoạt động vì Gateway Endpoint hiện tại có default policy cho phép truy cập đầy đủ (`"*"`) đến tất cả S3 resources. Bạn sắp thay đổi điều này để minh họa các hạn chế dựa trên policy.
{{% /notice %}}

---

#### Phần 2: Tạo S3 Bucket Thứ Hai để Kiểm Tra Policy

Bạn sẽ tạo bucket thứ hai để minh họa kiểm soát truy cập có chọn lọc.

**Bước 1: Điều Hướng đến S3 Console**

1. Mở [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home?region=us-east-1)
2. Click **Create bucket**

**Bước 2: Cấu Hình Bucket Mới**

1. **Bucket name**: 
   - Theo mẫu đặt tên hiện tại của bạn, nhưng thêm `-restricted`
   - Ví dụ: Nếu bucket đầu tiên của bạn là `my-endpoint-test-bucket-123456`, đặt tên bucket này là `my-endpoint-test-bucket-123456-restricted`
   - Phải là duy nhất toàn cầu trên tất cả các tài khoản AWS

2. **Region**: 
   - Đảm bảo là **US East (N. Virginia) us-east-1** (giống VPC và bucket đầu tiên của bạn)

3. **Các cài đặt khác**:
   - Để tất cả cài đặt khác ở giá trị mặc định
   - Default encryption: Enabled (SSE-S3)
   - Block Public Access: Enabled (khuyến nghị)
   - Versioning: Disabled (để đơn giản cho workshop)

4. **Create bucket**:
   - Cuộn xuống dưới và click **Create bucket**

![Tạo S3 Bucket Thứ Hai](/images/5-Workshop/5.5-Policy/create-bucket.png)

**Bước 3: Xác Minh Việc Tạo Bucket**

Bạn sẽ thấy thông báo thành công và bucket mới được liệt kê trong S3 console.

![Bucket Được Tạo Thành Công](/images/5-Workshop/5.5-Policy/create-bucket-success.png)

{{% notice tip %}}
**Chiến Lược Đặt Tên**: Sử dụng mẫu đặt tên nhất quán (tên gốc + hậu tố) giúp dễ dàng xác định các resources liên quan và đơn giản hóa việc xây dựng policy ARN. Trong production, sử dụng quy ước đặt tên phản ánh môi trường, ứng dụng và mục đích.
{{% /notice %}}

---

#### Phần 3: Áp Dụng Restrictive Endpoint Policy

Bây giờ bạn sẽ sửa đổi Gateway Endpoint policy để cho phép truy cập chỉ đến bucket mới.

**Bước 1: Điều Hướng đến VPC Endpoints**

1. Mở [VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)
2. Click **Endpoints** trong thanh bên trái
3. Tìm và chọn **Gateway Endpoint** bạn đã tạo trong phần 5.3
   - Type nên hiển thị "Gateway"
   - Service name nên là `com.amazonaws.us-east-1.s3`

**Bước 2: Truy Cập Endpoint Policy Editor**

1. Với endpoint được chọn, click vào tab **Policy** trong ngăn chi tiết dưới
2. Bạn sẽ thấy policy hiện tại (mặc định cho phép tất cả)
3. Click nút **Edit policy**

![Edit Endpoint Policy](/images/5-Workshop/5.5-Policy/policy1.png)

**Current Default Policy:**

Policy hiện tại trông như thế này (cho phép truy cập không hạn chế):
```json
{
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

**Bước 3: Áp Dụng Restrictive Policy**

1. **Xóa policy hiện tại** trong policy editor

2. **Sao chép và dán** restrictive policy sau:

```json
{
  "Version": "2012-10-17",
  "Id": "RestrictToSpecificBucketPolicy",
  "Statement": [
    {
      "Sid": "AllowAccessToRestrictedBucketOnly",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::YOUR-RESTRICTED-BUCKET-NAME",
        "arn:aws:s3:::YOUR-RESTRICTED-BUCKET-NAME/*"
      ]
    }
  ]
}
```

3. **Thay thế các ARN placeholder**:
   - Tìm `YOUR-RESTRICTED-BUCKET-NAME` (xuất hiện hai lần)
   - Thay thế bằng tên bucket thứ hai thực tế của bạn (bucket kết thúc bằng `-restricted`)
   - Kết quả ví dụ:
     ```json
     "Resource": [
       "arn:aws:s3:::my-endpoint-test-bucket-123456-restricted",
       "arn:aws:s3:::my-endpoint-test-bucket-123456-restricted/*"
     ]
     ```

4. **Lưu policy**:
   - Click nút **Save**

![Áp Dụng Custom Endpoint Policy](/images/5-Workshop/5.5-Policy/policy2.png)

**Hiểu Policy:**

Hãy phân tích từng thành phần:

- **`Version`**: Phiên bản ngôn ngữ IAM policy (luôn sử dụng "2012-10-17")
- **`Id`**: Định danh tùy chọn cho policy
- **`Statement`**: Mảng các policy statements (có thể có nhiều)
  - **`Sid`**: Statement ID (nhãn mô tả)
  - **`Effect`**: `Allow` (cấp quyền) hoặc `Deny` (từ chối rõ ràng)
  - **`Principal`**: `"*"` có nghĩa là bất kỳ IAM identity nào (vẫn tuân theo IAM policies)
  - **`Action`**: `s3:*` cho phép tất cả S3 operations (GetObject, PutObject, ListBucket, v.v.)
  - **`Resource`**: Hai ARNs:
    - ARN đầu tiên: Chính bucket (cho các operations như ListBucket)
    - ARN thứ hai với `/*`: Tất cả objects trong bucket (cho GetObject, PutObject, v.v.)

{{% notice success %}}
**Policy Đã Được Áp Dụng!** Gateway Endpoint bây giờ thực thi rằng chỉ restricted bucket có thể được truy cập thông qua nó. Tất cả các nỗ lực truy cập buckets khác (bao gồm bucket gốc của bạn) sẽ bị từ chối.
{{% /notice %}}

**Logic Đánh Giá Policy:**

Khi một EC2 instance cố gắng truy cập S3 qua endpoint:
```
1. IAM policy trên EC2 instance role: Phải Allow
2. VPC Endpoint policy: Phải Allow ← Chúng ta vừa hạn chế điều này
3. S3 bucket policy: Phải Allow (hoặc không Deny rõ ràng)
4. S3 ACLs: Phải Allow (hoặc không Deny rõ ràng)

Chỉ khi TẤT CẢ các kiểm tra pass → Quyền truy cập được cấp
```

---

#### Phần 4: Kiểm Tra Việc Thực Thi Policy

Bây giờ xác thực rằng endpoint policy đang thực thi các hạn chế truy cập.

**Bước 1: Kiểm Tra Truy Cập đến Bucket Gốc (Nên Fail)**

Trong Session Manager terminal vẫn mở của bạn trên Test-Gateway-Endpoint, thử liệt kê bucket gốc:

```bash
aws s3 ls s3://<your-original-bucket-name>
```

![Access Denied to Original Bucket](/images/5-Workshop/5.5-Policy/error.png)

**Kết Quả Mong Đợi:**

Bạn sẽ nhận được lỗi **Access Denied**:
```
An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied
```

{{% notice note %}}
**Tại Sao Access Denied?** VPC endpoint policy không còn bao gồm bucket gốc trong các resources được phép của nó. Mặc dù EC2 instance IAM role của bạn có S3 permissions, network path (qua endpoint) chặn truy cập.
{{% /notice %}}

**Bước 2: Chuẩn Bị Dữ Liệu Test**

Tạo một test file để upload lên restricted bucket:

1. **Điều hướng đến home directory**:
   ```bash
   cd ~
   ```

2. **Tạo test file 1GB**:
   ```bash
   fallocate -l 1G policy-test-file.dat
   ```

**Bước 3: Kiểm Tra Truy Cập đến Restricted Bucket (Nên Succeed)**

Upload file lên restricted bucket:

```bash
aws s3 cp policy-test-file.dat s3://<your-restricted-bucket-name>
```

Thay thế `<your-restricted-bucket-name>` bằng tên bucket thứ hai của bạn.

![Successful Upload to Restricted Bucket](/images/5-Workshop/5.5-Policy/test2.png)

**Kết Quả Mong Đợi:**

Upload nên thành công:
```
upload: ./policy-test-file.dat to s3://my-bucket-restricted/policy-test-file.dat
Completed 1.0 GiB/1.0 GiB
```

**Bước 4: Xác Minh File Upload trong S3 Console**

1. Quay lại **S3 Console**
2. Click vào **restricted bucket** của bạn
3. Bạn sẽ thấy object `policy-test-file.dat`

![File Successfully Uploaded](/images/5-Workshop/5.5-Policy/test2-success.png)

{{% notice success %}}
**Việc Thực Thi Policy Đã Được Xác Nhận!** Endpoint policy đã thành công:
- ✅ Cho phép truy cập đến restricted bucket
- ❌ Chặn truy cập đến bucket gốc

Điều này minh họa kiểm soát truy cập cấp mạng chi tiết.
{{% /notice %}}

**Bước 5: Xác Nhận Bucket Gốc Vẫn Bị Chặn**

Như một test cuối cùng, thử upload lên bucket gốc (nên fail):

```bash
aws s3 cp policy-test-file.dat s3://<your-original-bucket-name>
```

![Access Denied to Original Bucket](/images/5-Workshop/5.5-Policy/test2-fail.png)

**Kết Quả Mong Đợi:**

Lỗi access denied:
```
upload failed: ./policy-test-file.dat to s3://my-original-bucket/policy-test-file.dat
An error occurred (AccessDenied) when calling the PutObject operation: Access Denied
```

Điều này xác nhận endpoint policy đang thực thi hạn chế một cách nhất quán.

---

#### Hiểu Hành Vi Policy

**Tóm Tắt Các Mẫu Truy Cập:**

| Operation | Target | Kết Quả | Lý Do |
|-----------|--------|--------|--------|
| List objects | Original bucket | ❌ Denied | Không có trong danh sách Resource của endpoint policy |
| Upload file | Original bucket | ❌ Denied | Không có trong danh sách Resource của endpoint policy |
| List objects | Restricted bucket | ✅ Allowed | Được cho phép rõ ràng trong endpoint policy |
| Upload file | Restricted bucket | ✅ Allowed | Được cho phép rõ ràng trong endpoint policy |

**Cách Lưu Lượng Chảy:**

Khi EC2 truy cập S3 qua Gateway Endpoint với restrictive policy:

```
┌─────────────────────┐
│  EC2 Instance       │
│  Test-Gateway-EP    │
└──────────┬──────────┘
           │
           │ S3 API Request
           ▼
┌─────────────────────┐
│  Route Table        │
│  Định tuyến S3      │
│  traffic đến Gateway│
│  Endpoint           │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────────────────┐
│  Gateway VPC Endpoint           │
│  Đánh giá Endpoint Policy       │
│                                 │
│  IF bucket IN allowed list:     │
│    → Forward to S3              │
│  ELSE:                          │
│    → Return Access Denied       │
└─────────────────────────────────┘
           │
           │ (Nếu allowed)
           ▼
┌─────────────────────┐
│  Amazon S3 Service  │
│  (Backend)          │
└─────────────────────┘
```

**Quan Sát Chính:**

1. **Network-Level Enforcement**: Endpoint policy chặn lưu lượng trước khi nó đến S3
2. **Independent of IAM**: Ngay cả khi IAM policies cho phép truy cập, endpoint policy có thể chặn nó
3. **All-or-Nothing per Bucket**: Bạn không thể chọn lọc cho phép chỉ các operations nhất định (ví dụ: read-only); policy áp dụng cho tất cả allowed actions
4. **Principal Agnostic**: Với `"Principal": "*"`, hạn chế áp dụng cho tất cả identities sử dụng endpoint

---

#### Các Use Cases Production cho Endpoint Policies

**Kịch Bản Doanh Nghiệp:**

1. **Data Residency Compliance**:
   ```json
   {
     "Statement": [{
       "Effect": "Allow",
       "Principal": "*",
       "Action": "s3:*",
       "Resource": "arn:aws:s3:::eu-compliant-data-bucket/*",
       "Condition": {
         "StringEquals": {
           "s3:ExistingObjectTag/DataClassification": "EUPersonalData"
         }
       }
     }]
   }
   ```

2. **Prevent Data Exfiltration**:
   - Whitelist chỉ các corporate buckets được phê duyệt
   - Chặn truy cập đến personal hoặc external buckets
   - Audit endpoint policy changes với CloudTrail

3. **Multi-Tenant Environments**:
   ```json
   {
     "Statement": [{
       "Effect": "Allow",
       "Principal": "*",
       "Action": "s3:*",
       "Resource": [
         "arn:aws:s3:::tenant-a-data/*",
         "arn:aws:s3:::shared-resources/*"
       ]
     }]
   }
   ```

4. **Development vs Production Isolation**:
   - Dev VPC endpoint: Truy cập chỉ đến dev/test buckets
   - Prod VPC endpoint: Truy cập chỉ đến production buckets
   - Ngăn chặn cross-environment access ngẫu nhiên

**Các Mẫu Policy Nâng Cao:**

**Condition-Based Access:**
```json
{
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": ["s3:GetObject", "s3:PutObject"],
    "Resource": "arn:aws:s3:::sensitive-bucket/*",
    "Condition": {
      "StringEquals": {
        "aws:SourceVpce": "vpce-xxxxxxxx"
      }
    }
  }]
}
```

**Read-Only Access:**
```json
{
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": [
      "s3:GetObject",
      "s3:ListBucket"
    ],
    "Resource": [
      "arn:aws:s3:::readonly-bucket",
      "arn:aws:s3:::readonly-bucket/*"
    ]
  }]
}
```

---

#### Những Điểm Chính Cần Nhớ

**Những Gì Bạn Đã Hoàn Thành:**

✅ **Baseline Testing**: Xác minh truy cập không hạn chế với default endpoint policy  
✅ **Bucket Creation**: Tạo S3 bucket thứ hai để kiểm tra policy  
✅ **Policy Application**: Áp dụng restrictive endpoint policy giới hạn truy cập đến bucket cụ thể  
✅ **Enforcement Validation**: Xác nhận policy chặn unauthorized buckets và cho phép authorized bucket  
✅ **Security Best Practice**: Minh họa nguyên tắc least privilege ở cấp mạng  

**Các Nguyên Tắc Bảo Mật Được Minh Họa:**

1. **Defense in Depth**: Endpoint policies thêm lớp kiểm soát cấp mạng
2. **Least Privilege**: Chỉ cấp quyền truy cập cần thiết, không phải quyền rộng
3. **Explicit Allow**: Phương pháp whitelist (chỉ định những gì được phép, chặn mọi thứ khác)
4. **Network Segmentation**: Kiểm soát luồng dữ liệu ở cấp VPC endpoint
5. **Auditability**: Thay đổi policy được ghi log trong CloudTrail để tuân thủ

**Khuyến Nghị Production:**

- **Version Control**: Lưu endpoint policies trong Git với quy trình code review
- **Policy Testing**: Kiểm tra policies trong dev/staging trước khi triển khai production
- **Monitoring**: Thiết lập CloudWatch alarms cho AccessDenied errors cho biết vi phạm policy
- **Documentation**: Duy trì tài liệu rõ ràng về buckets nào có thể truy cập qua endpoints nào
- **Regular Review**: Audit endpoint policies hàng quý để loại bỏ quyền truy cập không cần thiết
- **Condition Keys**: Sử dụng IAM condition keys nâng cao cho hạn chế dựa trên thời gian, IP, hoặc tag
- **Deny Statements**: Cân nhắc explicit Deny statements cho yêu cầu bảo mật cao

Phần này đã minh họa cách VPC endpoint policies cung cấp kiểm soát truy cập quan trọng cho các kiến trúc cloud doanh nghiệp, cho phép bạn thực thi các chính sách bảo mật tổ chức ở cấp cơ sở hạ tầng mạng!
