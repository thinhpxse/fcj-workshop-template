---
title: "Event 1"
date: 2025-09-18
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---


# Bài thu hoạch “Cloud Day Vietnam 2025 : AI Edition”

### Mục Đích Của Sự Kiện

- Khai thác đổi mới cloud và AI , và cùng nhau thảo luận về VietNam trong tươi lai và kỉ nguyên chuyển đổi AI sắp tới.
- Cho sinh viên và các học hỏi từ các chuyên gia về chuyển đổi công nghệ hiện nay cùng với đối tác Amazon Web Service (AWS)
### Danh Sách Diễn Giả

- **Pham Duc Long** Deputy Minister ò Science and Technology
- **Jef Johnson** - Managing Director ASEAN,AWS
- **H.E.Marc E.Knapper** - U.S.Ambassador to the Socialist Republic of VietNam
- **Dr Jens Lottner** - Chief Executive Officer Techcombank
- **Dieter Botha** - CEO , Tymex
- **Jaime Valles** - Vice President, General Manager Asia Pacific and Japan , AWS
- **Trang Phung** - CEO, U2U Network 
- **VU VAN** - Co-founder & CEO , ELSA Corp
- **Nguyen Hoa Binh** - Chairman, Nexttech Group

### Nội Dung Nổi Bật

#### 

- Tổng quan kinh tế & năng xuất lao động Vtệt Nam 
- Xu hướng công nghệ & chuyển đổi số tại Việt Nam
- Các chiến lược cảu techcombank 
- Tác động AI tới Việt Nam
- Ứng dụng AI trong giáo dục 

#### Chuyển đổi sang kiến trúc ứng dụng mới - Microservice Architecture

Chuyển đổi thành hệ thống modular – từng chức năng là một **dịch vụ độc lập** giao tiếp với nhau qua **sự kiện** với 3 trụ cột cốt lõi:

- **Queue Management**: Xử lý tác vụ bất đồng bộ
- **Caching Strategy:** Tối ưu performance
- **Message Handling:** Giao tiếp linh hoạt giữa services

#### Domain-Driven Design (DDD)

- **Phương pháp 4 bước**: Xác định domain events → sắp xếp timeline → identify actors → xác định bounded contexts
- **Case study bookstore**: Minh họa cách áp dụng DDD thực tế
- **Context mapping**: 7 patterns tích hợp bounded contexts

#### Event-Driven Architecture

- **3 patterns tích hợp**: Publish/Subscribe, Point-to-point, Streaming
- **Lợi ích**: Loose coupling, scalability, resilience
- **So sánh sync vs async**: Hiểu rõ trade-offs (sự đánh đổi)

#### Compute Evolution

- **Shared Responsibility Model**: Từ EC2 → ECS → Fargate → Lambda
- **Serverless benefits**: No server management, auto-scaling, pay-for-value
- **Functions vs Containers**: Criteria lựa chọn phù hợp

#### Amazon Q Developer

- **SDLC automation**: Từ planning đến maintenance
- **Code transformation**: Java upgrade, .NET modernization
- **AWS Transform agents**: VMware, Mainframe, .NET migration

### Những Gì Học Được

#### Tư Duy Thiết Kế

- **Business-first approach**: Luôn bắt đầu từ business domain, không phải technology
- **Ubiquitous language**: Importance của common vocabulary giữa business và tech teams
- **Bounded contexts**: Cách identify và manage complexity trong large systems

#### Kiến Trúc Kỹ Thuật

- **Event storming technique**: Phương pháp thực tế để mô hình hóa quy trình kinh doanh
- Sử dụng **Event-driven communication** thay vì synchronous calls
- **Integration patterns**: Hiểu khi nào dùng sync, async, pub/sub, streaming
- **Compute spectrum**: Criteria chọn từ VM → containers → serverless

#### Chiến Lược Hiện Đại Hóa

- **Phased approach**: Không rush, phải có roadmap rõ ràng
- **7Rs framework**: Nhiều con đường khác nhau tùy thuộc vào đặc điểm của mỗi ứng dụng
- **ROI measurement**: Cost reduction + business agility

### Ứng Dụng Vào Công Việc

- **Áp dụng DDD** cho project hiện tại: Event storming sessions với business team
- **Refactor microservices**: Sử dụng bounded contexts để identify service boundaries
- **Implement event-driven patterns**: Thay thế một số sync calls bằng async messaging
- **Serverless adoption**: Pilot AWS Lambda cho một số use cases phù hợp
- **Try Amazon Q Developer**: Integrate vào development workflow để boost productivity

### Trải nghiệm trong event

Tham gia workshop **“GenAI-powered App-DB Modernization”** là một trải nghiệm rất bổ ích, giúp tôi có cái nhìn toàn diện về cách hiện đại hóa ứng dụng và cơ sở dữ liệu bằng các phương pháp và công cụ hiện đại. Một số trải nghiệm nổi bật:

#### Học hỏi từ các diễn giả có chuyên môn cao
- Các diễn giả đến từ AWS và các tổ chức công nghệ lớn đã chia sẻ **best practices** trong thiết kế ứng dụng hiện đại.
- Qua các case study thực tế, tôi hiểu rõ hơn cách áp dụng **Domain-Driven Design (DDD)** và **Event-Driven Architecture** vào các project lớn.

#### Trải nghiệm kỹ thuật thực tế
- Tham gia các phiên trình bày về **event storming** giúp tôi hình dung cách **mô hình hóa quy trình kinh doanh** thành các domain events.
- Học cách **phân tách microservices** và xác định **bounded contexts** để quản lý sự phức tạp của hệ thống lớn.
- Hiểu rõ trade-offs giữa **synchronous và asynchronous communication** cũng như các pattern tích hợp như **pub/sub, point-to-point, streaming**.

#### Ứng dụng công cụ hiện đại
- Trực tiếp tìm hiểu về **Amazon Q Developer**, công cụ AI hỗ trợ SDLC từ lập kế hoạch đến maintenance.
- Học cách **tự động hóa code transformation** và pilot serverless với **AWS Lambda**, từ đó nâng cao năng suất phát triển.

#### Kết nối và trao đổi
- được tham gia nghe các chuyên gia hàng đầu của Việt Nam về bảo mật và chuyển đổi mới về AI
- Trực tiếp học hỏi cách bảo mật hiện nay và các dự tính tương lai các chuyển đổi công nghệ từ AI Specualist OCB **Ngan Nguyen Minh** 


#### Bài học rút ra
- AI và Blockchain là thứ cốt lõi để chuyển đổi mới và sáng tạo , nó có thể đảm bảo được bảo mật và trải nghiệm của khách hàng , là mục tiêu quan trọng có việc làm trong tương lai.
- Cần có vai trò to lớn của cộng đồng Công nghệ của Việt Nam và sự đầu tư của AWS để tạo thêm nhiều cơ hội cho thế hệ trẻ của Việt Nam .
- Cần có tư duy học tập liên tục để có thể nắm bắt được các công nghệ mới và phát triển , phải nắm được các cơ hội nghề nghiệp mà các công ty lớn của Việt Nam cũng như nước ngoài .
- Cần có các mục tiêu lớn với tầm nhìn toàn cầu để có thể chuyển mình trong tương lai dài . 

#### Một số hình ảnh khi tham gia sự kiện
* Thêm các hình ảnh của các bạn tại đây
> Tổng thể, sự kiện không chỉ cung cấp kiến thức kỹ thuật mà còn giúp tôi thay đổi cách tư duy về cloud AI , hiện đại hóa hệ thống và phối hợp hiệu quả hơn giữa các Công ty , tầm quan trọng về chuyển đổi số , thấy được tầm nhìn về công nghệ hiện nay của Việt Nam và thế giới .