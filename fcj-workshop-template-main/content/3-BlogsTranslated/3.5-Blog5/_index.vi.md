---
title: "Blog 5"
weight: 1
chapter: false
pre: " <b> 3.5 </b> "
---
{{% notice warning %}}
⚠️ **Lưu ý:** Thông tin dưới đây chỉ nhằm mục đích tham khảo. Vui lòng **không sao chép nguyên văn** vào báo cáo của bạn, bao gồm cả phần này.
{{% /notice %}}

# Xây dựng AI Gateway cho Amazon Bedrock với Amazon API Gateway

Khi doanh nghiệp xây dựng ứng dụng Generative AI ở quy mô lớn, họ cần một lớp kiểm soát tập trung nhằm quản lý việc sử dụng Foundation Models — như xác thực, giới hạn truy cập, phân tách người dùng/tenant, theo dõi chi phí, và điều phối luồng gọi API. Bài blog giới thiệu một mô hình tham chiếu sử dụng **Amazon API Gateway** như một lớp “AI Gateway” đứng trước **Amazon Bedrock**, cho phép triển khai quản trị AI ở cấp độ doanh nghiệp mà không làm thay đổi cách ứng dụng gọi API.  
([aws.amazon.com](https://aws.amazon.com/blogs/architecture/building-an-ai-gateway-to-amazon-bedrock-with-amazon-api-gateway/?utm_source=chatgpt.com))

---

## Tổng quan kiến trúc AI Gateway

Mô hình kiến trúc cung cấp khả năng kiểm soát chi tiết đối với việc truy cập Foundation Models, sử dụng hoàn toàn các dịch vụ AWS managed.

Các thành phần chính:

- **Custom domain (tùy chọn):** sử dụng DNS (ví dụ Amazon Route 53) để ánh xạ domain riêng của công ty vào API Gateway, giúp client gọi API bằng domain nội bộ.  
- **Amazon API Gateway:** là điểm vào duy nhất cho mọi cuộc gọi tới Bedrock — xử lý xác thực, kiểm soát tốc độ, quota usage, versioning, WAF, logging và tích hợp bảo mật.  
- **Lambda Authorizer:** kiểm tra token (ví dụ JWT) hoặc tích hợp với hệ thống IAM/identity hiện có, nhằm thực thi phân quyền theo từng user/tenant.  
- **Lambda Integration (request forwarder):** nhận request, ký lại request bằng AWS Signature v4, sau đó chuyển tới endpoint Amazon Bedrock. Đây chính là lớp giữ nguyên khả năng dùng SDK chuẩn của Bedrock mà không phải chỉnh sửa client.  
- **Amazon Bedrock:** cung cấp các Foundation Models và dịch vụ Generative AI phía sau Gateway.

Nhờ cách tiếp cận này, client có thể tiếp tục dùng SDK (ví dụ boto3) để gọi Bedrock như thường, nhưng mọi request đều được kiểm soát qua Gateway.  
([aws.amazon.com](https://aws.amazon.com/blogs/architecture/building-an-ai-gateway-to-amazon-bedrock-with-amazon-api-gateway/?utm_source=chatgpt.com))

---

## Triển khai & kiểm tra Gateway

- Bài blog cung cấp CloudFormation template để triển khai hạ tầng Gateway.  
- Ở bước thử nghiệm ban đầu, có thể **tắt phần xác thực** (JWT Authorizer) và chỉ kiểm tra connectivity trong môi trường private.  
- Sau khi triển khai stack, CloudFormation trả về các output như:  
  - `GatewayUrl`  
  - `VpcId`  
  - `ApiId`  
  Các giá trị này dùng để gọi thử API từ trong VPC (ví dụ bằng AWS CloudShell).  
- Blog cũng cung cấp **đoạn mã Python (boto3)** để tạo client gọi qua API Gateway nhưng vẫn hoạt động giống gọi Bedrock trực tiếp.  
- API Gateway hỗ trợ **streaming response**, nên có thể stream output của mô hình LLM theo thời gian thực — phù hợp cho chatbot, agent và ứng dụng thời gian thực.  
- Khi đã kiểm thử thành công, có thể **bật lại JWT Authorizer** bằng cách cập nhật tham số CloudFormation rồi redeploy API Gateway.  
([aws.amazon.com](https://aws.amazon.com/blogs/architecture/building-an-ai-gateway-to-amazon-bedrock-with-amazon-api-gateway/?utm_source=chatgpt.com))

---

## Nâng cao khả năng quản trị qua AI Gateway

Việc đưa API Gateway làm lớp trước Bedrock mang lại nhiều khả năng quản lý:

- **Rate limiting & throttling:** kiểm soát lưu lượng theo từng tenant/user.  
- **Quota management:** giới hạn số lần gọi, đảm bảo cost-control.  
- **Lifecycle & version management:** hỗ trợ canary release, rollout theo stage, version API tách biệt.  
- **Tích hợp AWS WAF:** bảo vệ khỏi các tấn công web phổ biến.  
- **Caching:** cache prompt/response hoặc các request lặp lại để giảm chi phí và latency.  
- **Content filtering / validation:** lớp Lambda forwarder có thể kiểm tra thêm nội dung trước/sau khi gửi tới Bedrock, bổ sung kiểm soát cho yêu cầu bảo mật doanh nghiệp.

---

## Lợi ích & trường hợp sử dụng

AI Gateway phù hợp cho các kịch bản:

- **Nền tảng AI đa-tenant**: mỗi tenant có quota và quyền truy cập riêng.  
- **Kiểm soát chi phí Generative AI**: theo dõi rate, số token, số request trên từng user.  
- **Tích hợp với hệ thống IAM có sẵn**: nhờ JWT hoặc custom authorizer.  
- **Đảm bảo tính tương thích SDK**: client không cần đổi mã nguồn, vẫn dùng SDK Bedrock như thông thường.  
- **Quy trình release an toàn**: canary, A/B testing, tách stage dev/prod.

Nói cách khác, AI Gateway giúp doanh nghiệp đưa Generative AI vào môi trường production một cách an toàn, kiểm soát và dễ mở rộng.

---

## Kết luận

Mô hình “AI Gateway cho Amazon Bedrock” cung cấp một phương pháp mạnh mẽ và an toàn để doanh nghiệp triển khai Generative AI. API Gateway đứng trước Bedrock giúp kiểm soát chi tiết ai được phép gọi model nào, gọi bao nhiêu, và theo cơ chế nào — đồng thời giữ nguyên tính tương thích SDK và trải nghiệm client.

Giải pháp này tạo nền tảng vững chắc để xây dựng:
- các nền tảng AI nội bộ,  
- dịch vụ AI đa-tenant,  
- hoặc backend AI quy mô lớn phù hợp với doanh nghiệp.
