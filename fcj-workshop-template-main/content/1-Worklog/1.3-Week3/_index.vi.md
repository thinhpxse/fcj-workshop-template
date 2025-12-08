---
title: "Week 3"

weight: 1
chapter: false
pre: " <b> 1.3 </b> "
---
{{% notice warning %}}
⚠️ Lưu ý: Đây là bản mẫu tham khảo, không sao chép nguyên văn cho báo cáo nộp chính thức.
{{% /notice %}}

### Mục tiêu tuần này:

* Hiểu tổng quan về các dịch vụ hỗ trợ Migration trên AWS.
* Nắm được luồng di chuyển hệ thống từ On-premises hoặc Cloud khác sang AWS.
* Biết vai trò của từng dịch vụ Migration (DMS, SCT, VM Import/Export, Elastic Disaster Recovery…).
* Chuẩn bị cơ bản cho các bài thực hành Migration ở tuần sau.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                     | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                             |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------ |
| 2   | - Đọc tổng quan về chuyên mục **Migrate to AWS** <br> - Xác định các nhóm dịch vụ Migration chính                                                             | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 3   | - Tìm hiểu **VM Migration với AWS VM Import/Export** <br> &emsp; + Cách import VM <br> &emsp; + Các định dạng hỗ trợ                                          | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 4   | - Nghiên cứu **Database Migration** với: <br> &emsp; + AWS Database Migration Service (DMS) <br> &emsp; + Schema Conversion Tool (SCT)                       | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 5   | - Đọc và ghi chú về **AWS Elastic Disaster Recovery** (DR) <br> &emsp; + Cách hoạt động <br> &emsp; + Cơ chế giảm downtime                                    | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |
| 6   | - Tìm hiểu các dịch vụ “Coming Soon”: <br> &emsp; + AWS Migration Hub <br> &emsp; + AWS Application Migration Service <br> &emsp; + AWS Migration Evaluator <br> &emsp; + AWS Application Discovery Service | dd/mm/2025   | dd/mm/2025      | https://cloudjourney.awsstudygroup.com/    |


### Kết quả đạt được:

* Nắm được toàn bộ danh mục dịch vụ trong nhóm **Migrate to AWS**, gồm:
  - AWS VM Import/Export
  - AWS Database Migration Service (DMS)
  - Schema Conversion Tool (SCT)
  - AWS Elastic Disaster Recovery
  - AWS Appl

* Hiểu rõ phân loại Migration:
  - **VM Migration**: Di chuyển máy ảo.
  - **Database Migration**: Di chuyển database + chuyển đổi schema.
  - **Disaster Recovery**: Chuẩn bị hệ thống phục hồi sau sự cố.
  - **Full Application Migration**: (các dịch vụ sắp ra mắt).

* Biết được mục tiêu từng dịch vụ:
  - **VM Import/Export** → Di chuyển VM từ VMware/Hyper-V sang EC2.
  - **DMS*ication Discovery Service (Coming Soon)* → Di chuyển Data minimal downtime.
  - **SCT** → Chuyển đổi schema khi chuyển engine (Oracle → Aurora…).
  - **Elastic Disaster Recovery** → Tạo bản sao sẵn sàng failover.
  - **Migration Hub** → Theo dõi toàn bộ project Migration.
  - **Application Migration Service** → Convert máy On-prem sang EC2 tự động.

* Đã ghi chú được các “Use cases” cho từng dịch vụ và sự khác nhau giữa Migration & Disaster Recovery.

* Chuẩn bị tài liệu cho buổi thực hành Migration tuần tới.
