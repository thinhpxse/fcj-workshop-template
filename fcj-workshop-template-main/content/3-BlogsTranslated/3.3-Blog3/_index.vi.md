---
title: "Blog 3"
weight: 1
chapter: false
pre: " <b> 3.3 </b> "
---
{{% notice warning %}}
⚠️ **Lưu ý:** Thông tin dưới đây chỉ nhằm mục đích tham khảo. Vui lòng **không sao chép nguyên văn** cho báo cáo của bạn, bao gồm cả phần này.
{{% /notice %}}

# Electronic Arts tối ưu hóa quy trình cập nhật game với AWS

Quy trình cập nhật (patching) game luôn là thách thức lớn: dung lượng game có thể lên đến hàng chục hoặc hàng trăm GB, việc so sánh dữ liệu trên máy người chơi thường chậm và dẫn đến kích thước patch lớn, thời gian cài đặt dài và trải nghiệm kém. Bài blog giới thiệu cách Electronic Arts (EA) sử dụng AWS để hiện đại hóa quy trình patching, giảm kích thước bản cập nhật, tăng tốc tải xuống và cải thiện trải nghiệm người chơi.

---

## Thách thức ban đầu: Patching chậm và kém hiệu quả

- Patching truyền thống chỉ so sánh file theo tên, khiến dữ liệu bị di chuyển giữa các file bị coi như “hoàn toàn mới” → người chơi phải tải lại nhiều GB dữ liệu.  
- Kích thước khối dữ liệu (block size) dùng để so sánh trên máy khách rất lớn (như 64 KB), làm giảm khả năng tái sử dụng dữ liệu cũ, khiến patch phình to.  
- Việc tính toán patch diễn ra trên máy người chơi mất nhiều phút hoặc lâu hơn, đặc biệt với các game lớn — ảnh hưởng trải nghiệm.

---

## Giải pháp: Known Version Patching (KVP) chạy trên AWS

Tháng 8/2023, EA giới thiệu hệ thống **Known Version Patching (KVP)** — quy trình tính toán patch trên server (server-side) chạy hoàn toàn trên AWS.

### KVP hoạt động như sau:
- Mỗi phiên bản game được tải về và giải nén trên AWS; hệ thống tính toán hash cho từng phiên bản.  
- So sánh metadata hash của phiên bản mới với tất cả phiên bản cũ để xác định chính xác phần dữ liệu thay đổi.  
- Tạo các bản patch incremental (nhỏ chỉ chứa dữ liệu thay đổi).  
- Một microservice điều phối (coordinator) nhận sự kiện khi có phiên bản mới, sau đó kích hoạt pipeline patching và các job xử lý song song.  
- Amazon Elastic Kubernetes Service (EKS) dùng để chạy các job song song quy mô lớn.  
- Amazon Elastic File System (EFS) lưu trữ bản build đã giải nén, cho phép tái sử dụng và tránh tải lại hoặc tính toán lại cho các phiên bản cũ.

---

## Kiến trúc kỹ thuật & quy trình patch

- Khi có version mới, hệ thống streaming (ví dụ Amazon MSK) gửi thông báo để kích hoạt pipeline.  
- Service điều phối tạo các job EKS để tải, giải nén, tính hash, so sánh metadata và tạo patch.  
- Patch được upload lên CDN để phân phối tới người chơi qua ứng dụng EA.  
- EFS cache lại các bản build đã giải nén – lần patch sau chỉ so sánh phần thay đổi → tiết kiệm thời gian và tài nguyên.  
- EKS dùng CPU affinity, autoscaling và tối ưu scheduling để tăng tối đa thông lượng của cluster.

---

## Lợi ích & cải thiện đạt được

- **Giảm kích thước patch:** Nhờ chỉ chứa dữ liệu thay đổi, patch giảm đến **~80%** so với trước.  
- **Tải xuống và cài đặt nhanh hơn:** Người chơi tải và cài đặt patch nhanh hơn **tối đa 3.6 lần**.  
- **Cải thiện trải nghiệm người chơi:** Ít chờ đợi hơn, giảm khó chịu khi cập nhật game.  
- **Tối ưu chi phí & tài nguyên:** Dựa trên hạ tầng AWS, EA mở rộng linh hoạt thay vì phụ thuộc vào khả năng máy người chơi.  
- **Tăng tốc phát triển:** Tự động hóa patch giúp chu kỳ phát hành và kiểm thử nhanh hơn nhiều.

---

## Kết luận

Bằng cách chuyển quá trình tính toán patch lên AWS và áp dụng hệ thống KVP (EKS + EFS), EA đã tạo ra bước nhảy vọt trong hiệu quả cập nhật game: bản cập nhật nhỏ hơn, nhanh hơn, tiết kiệm chi phí và ổn định hơn. Người chơi nhận patch nhanh hơn, trong khi EA có thể rút ngắn thời gian phát triển và giảm tải cho hạ tầng nội bộ.

Giải pháp này chứng minh sức mạnh của AWS trong việc xử lý khối lượng dữ liệu lớn, quy mô toàn cầu và cung cấp trải nghiệm vượt trội cho cả nhà phát triển lẫn game thủ.
