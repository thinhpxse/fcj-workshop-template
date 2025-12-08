---
title: "Bảo mật Amazon EVS với AWS Network Firewall"
weight: 1
chapter: false
pre: " <b> 3.4 </b> "
---
{{% notice warning %}}
⚠️ **Lưu ý:** Thông tin dưới đây chỉ để tham khảo. Vui lòng **không sao chép nguyên văn** cho báo cáo của bạn, bao gồm cả phần này.
{{% /notice %}}

# Bảo mật Amazon EVS với AWS Network Firewall

Amazon EVS cho phép tổ chức di chuyển, chạy và mở rộng khối lượng công việc VMware một cách native trên AWS — sử dụng VMware Cloud Foundation (VCF) chạy bên trong một Amazon VPC trên các instance bare-metal Amazon EC2. Giải pháp giúp rút ngắn thời gian di chuyển, dễ mở rộng và giữ nguyên cấu hình ứng dụng mà không cần refactor. :contentReference[oaicite:5]{index=5}

Khi thiết kế kiến trúc hybrid cloud (kết hợp EVS, VPC, data center tại chỗ, và kết nối internet), cần có một giải pháp bảo mật mạng thống nhất để bảo vệ lưu lượng ứng dụng — đảm bảo kiểm soát firewall, quản lý chính sách tập trung, logging/monitoring tập trung. Ở đây, AWS đề xuất dùng **AWS Network Firewall**. :contentReference[oaicite:6]{index=6}

Bài viết này hướng dẫn cách sử dụng AWS Network Firewall để **bảo mật môi trường EVS** — áp dụng mô hình “centralized inspection” để giám sát & lọc lưu lượng qua EVS cluster, VPCs, data center on-prem, và internet. :contentReference[oaicite:7]{index=7}

---

## Tổng quan kiến trúc

- AWS Network Firewall hoạt động như một “bump-in-the-wire” — được chèn trực tiếp vào đường truyền mạng (qua route table của VPC hoặc Transit Gateway), nên có thể kiểm tra mọi gói tin đi qua mà không cần thay đổi mô hình ứng dụng. :contentReference[oaicite:8]{index=8}  
- Mô hình minh hoạ gồm:  
   - Một VPC dành cho EVS (EVS VPC), chứa các subnet VLAN underlay cho VCF (tương ứng host management, vMotion, vSAN, overlay mạng NSX, …). :contentReference[oaicite:9]{index=9}  
   - Một hoặc nhiều VPC workload tiêu chuẩn (ví dụ VPC01), nơi ứng dụng hoặc dịch vụ khác hoạt động. :contentReference[oaicite:10]{index=10}  
   - Một VPC ingress để nhận traffic từ internet (kèm Application Load Balancer), và một VPC egress với NAT gateway để xử lý xuất Internet — giúp tập trung egress và ingress. :contentReference[oaicite:11]{index=11}  
   - Nếu có kết nối tới data center on-premises (qua AWS Direct Connect + Direct Connect Gateway), kết nối này cũng đi qua Firewall để giám sát. :contentReference[oaicite:12]{index=12}  

Mọi luồng traffic — từ EVS ↔ Workload VPC, giữa các VPC, giữa VPCs và on-premises, hoặc VPC ↔ Internet — đều được AWS Network Firewall kiểm tra (“east-west” & “north-south”). :contentReference[oaicite:13]{index=13}

### Lợi ích chính

- Một điểm kiểm soát (single point) để quản lý chính sách firewall cho toàn bộ mạng — VPCs, on-prem, internet. :contentReference[oaicite:14]{index=14}  
- Thống nhất rule enforcement, dễ quản lý hơn khi môi trường phức tạp. :contentReference[oaicite:15]{index=15}  
- Logging & monitoring tập trung — giúp ghi lại log firewall, flow, alert … cho việc giám sát và audit. :contentReference[oaicite:16]{index=16}  

---

## Các bước triển khai (tóm tắt)

1. **Tạo một AWS Transit Gateway** — không bật association/propagation route table mặc định. Tạo hai bảng route table: Pre-inspection và Post-inspection route table. :contentReference[oaicite:18]{index=18}  
2. **Gắn (attach) tất cả VPC (EVS, workload, ingress, egress) — và nếu cần Direct Connect Gateway — vào Transit Gateway.** Đảm bảo các attachment dùng bảng Pre-inspection. :contentReference[oaicite:19]{index=19}  
3. **Tạo AWS Network Firewall với Transit Gateway native integration.** Điều này giúp AWS tự provision các resource cần thiết, giảm overhead quản lý VPC/subnet/endpoint firewall. :contentReference[oaicite:20]{index=20}  
4. **Cập nhật route table:**  
   - Pre-inspection route table: default route đưa traffic tới Firewall (để kiểm tra trước khi đi tiếp) :contentReference[oaicite:21]{index=21}  
   - Post-inspection route table: định tuyến traffic sau khi kiểm tra trở về đúng VPC/người nhận. :contentReference[oaicite:22]{index=22}  
   - Trong mỗi VPC, route egress/ingress phù hợp để đảm bảo mọi traffic đều đi qua Firewall. :contentReference[oaicite:23]{index=23}  
5. **(Tùy chọn) Cấu hình rule firewall & logging:** Ví dụ rule stateful để lọc egress theo FQDN, rule kiểm soát traffic HTTP/HTTPS, rule lọc east-west, etc. Firewall log có thể gửi về Amazon CloudWatch Logs hoặc Amazon S3 để lưu. :contentReference[oaicite:26]{index=26}  

Khi cấu hình đúng, mọi luồng traffic — từ EVS, VPC khác, on-prem, internet — đều được giám sát & kiểm soát, nâng cao an ninh mạng tổng thể.

---

## Ưu điểm so với phương pháp bảo mật truyền thống

- Không cần thay đổi kiến trúc ứng dụng (không “in-VM firewall”, không cần thay đổi hostname/IP) — firewall hoạt động ở tầng mạng, minh bạch với ứng dụng.  
- Quản lý firewall tập trung, dễ áp dụng chính sách bảo mật đồng nhất cho nhiều VPC/cluster/hệ thống, phù hợp với môi trường hybrid cloud.  
- Có khả năng mở rộng linh hoạt, tự động, nhờ AWS managed infra — phù hợp với tải lớn, nhiều kết nối.  
- Logging, giám sát tập trung — thuận tiện cho compliance, audit, phân tích sự cố.  

---

## Kết luận

Bài viết này minh hoạ cách tích hợp AWS Network Firewall để bảo mật môi trường EVS, tạo ra một **mô hình kiểm tra mạng tập trung (centralized inspection)** — bảo vệ lưu lượng giữa EVS, các VPC khác, data-center on-prem và Internet. Với phương án này, tổ chức có thể di chuyển khối lượng công việc VMware lên AWS, đồng thời duy trì an toàn mạng, kiểm soát dễ dàng, logs & monitoring tập trung.  

Giải pháp giúp đảm bảo rằng migration lên AWS không phải đánh đổi giữa lợi ích cloud & rủi ro bảo mật — mà vẫn có thể đạt cả hai.  
