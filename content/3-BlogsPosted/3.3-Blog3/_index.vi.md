---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Cách Sonar xây dựng unified API trên AWS

Khi đọc bài viết “How Sonar built a unified API on AWS” trên AWS Architecture Blog, điểm mình thấy đáng chú ý nhất là cách Sonar giải quyết một bài toán rất thực tế khi hiện đại hóa hệ thống: làm sao chuyển từ kiến trúc monolithic sang nhiều domain độc lập, nhưng vẫn expose dịch vụ ra bên ngoài thông qua một API thống nhất, bảo mật và dễ vận hành.

**Bài toán kiến trúc**

Nếu mỗi domain có một public API riêng, hệ thống có thể trở nên khó quản lý hơn, đồng thời làm tăng attack surface. Với một sản phẩm SaaS được sử dụng bởi nhiều tổ chức như SonarCloud, việc expose API cần đảm bảo ba yếu tố chính:
- Người dùng bên ngoài có một endpoint thống nhất để truy cập.
- Các team bên trong vẫn có thể triển khai domain của mình độc lập.
- Kiến trúc phải bảo mật, có khả năng mở rộng và giảm gánh nặng vận hành cho platform team.

Giải pháp của Sonar là kết hợp Elastic Load Balancing, AWS PrivateLink và Amazon API Gateway để tạo ra một unified API endpoint.

**Tổng quan giải pháp**

- Trong kiến trúc này, người dùng truy cập API thông qua một domain chung, ví dụ: api.example.com

- Phía sau domain này là một Application Load Balancer (ALB). ALB được cấu hình HTTPS listener và forward request tới các Elastic Network Interfaces, hay ENIs, của VPC endpoint dành cho API Gateway.

- VPC endpoint cho API Gateway được triển khai trên ba Availability Zones. Mỗi private subnet có một ENI tương ứng. ALB target group chứa IP addresses của các ENIs này.

- Ở phía API Gateway, Sonar tạo một custom domain là api.example.com. Bên trong custom domain này, họ cấu hình API mappings cho từng domain. Ví dụ: api.example.com/domain1 sẽ được route tới private API tương ứng của domain1 trong API Gateway.

- Cách thiết kế này giúp bên ngoài chỉ nhìn thấy một API endpoint thống nhất, trong khi bên trong mỗi domain vẫn có thể có private API Gateway riêng.

**Luồng request hoạt động như thế nào?**

Luồng xử lý request trong kiến trúc này có thể hiểu theo các bước sau.
1. Đầu tiên, người dùng gửi request tới: api.example.com/domain1. Thông qua Amazon Route 53, domain này được phân giải DNS và route request tới Application Load Balancer.
2. Tiếp theo, ALB nhận request HTTPS, terminate connection và decrypt request. Sau đó, ALB gửi request tới một trong các ENIs của VPC endpoint dành cho API Gateway. Tại thời điểm này, domain name của request vẫn là: api.example.com và path của request là: /domain1
3. API Gateway sử dụng custom domain name và API mapping để xác định request này phải đi tới private API nào. Với path /domain1, request sẽ được route tới private API của domain1.
Từ góc nhìn của người dùng, ta chỉ gọi một endpoint chung. Nhưng từ góc nhìn kiến trúc nội bộ, request được điều hướng tới đúng domain tương ứng.

**Hai tính năng quan trọng của API Gateway**

1. Private REST API trong Amazon API Gateway chỉ có thể được truy cập từ bên trong VPC thông qua một interface VPC endpoint. Interface endpoint này tạo ENI trong VPC.
2. API Gateway custom domains. Mặc định, một API trong API Gateway có URL dạng: https://api-id.execute-api.region.amazonaws.com/stage Với custom domain, có thể tạo URL dễ hiểu hơn, ví dụ: https://api.example.com/domain1

Tính năng này không được hỗ trợ cho private REST APIs theo cách mặc định. Vì vậy, giải pháp này dùng một workaround đã được tài liệu hóa trong https://github.com/aws-samples/.
Sonar không expose từng private API trực tiếp ra internet, mà dùng ALB, VPC endpoint và API Gateway mapping để tạo một lớp unified API bên ngoài, đồng thời giữ các API domain phía sau ở chế độ private.

**Vì sao kiến trúc này hữu ích?**

- Điểm hay của kiến trúc là nó giải quyết cùng lúc hai nhu cầu tưởng như mâu thuẫn.
- Một mặt, khách hàng cần một API public đơn giản, dễ sử dụng và nhất quán. Họ không cần biết phía sau có bao nhiêu domain hay bao nhiêu team đang vận hành.
- Mặt khác, các team nội bộ của Sonar vẫn có thể phát triển, triển khai và vận hành domain riêng của mình một cách độc lập. Mỗi domain có thể có private API Gateway riêng, phù hợp với cách tổ chức theo Domain Driven Design.

### Đánh giá theo AWS Well-Architected Framework

### Security

- Unified API giúp giảm attack surface so với việc tạo một public API riêng cho từng domain. Thay vì expose nhiều endpoint public, hệ thống tập trung truy cập qua một entry point duy nhất. Việc sử dụng AWS WAF trên ALB để bảo vệ ứng dụng khỏi các web exploits phổ biến. Ngoài ra, AWS Shield, được bật mặc định trên Amazon CloudFront, cung cấp lớp bảo vệ ở Network/Transport layer trước các cuộc tấn công DDoS.

### Operational Excellence

- Kiến trúc này cho phép từng team triển khai thay đổi ứng dụng và hạ tầng phía sau private API Gateway riêng. Điều này giúp giảm operational overhead cho platform team, vì không cần quản lý chi tiết từng domain. Ngoài ra, giải pháp dựa trên các managed services của AWS, nên có thể tự động scale khi mức sử dụng SonarCloud tăng lên.

### Reliability

- Giải pháp được xây dựng trên các dịch vụ AWS có khả năng high availability mặc định trên nhiều Availability Zones trong cùng một Region. Request throttling có thể được cấu hình trên từng private API Gateway để bảo vệ tài nguyên phía sau khỏi tình trạng bị quá tải.

### Performance

- Amazon CloudFront giúp cải thiện hiệu năng API, đặc biệt với người dùng ở xa Region triển khai. Traffic đi qua AWS network backbone, giúp tối ưu đường truyền khi truy cập tới ALB.
Cost
- ALB được sử dụng như một entry point duy nhất. Cách này có thể phát sinh thêm chi phí so với việc expose nhiều public API Gateway trực tiếp. Tuy nhiên, đây là một trade-off để đổi lấy bảo mật tốt hơn và trải nghiệm khách hàng nhất quán hơn.

### Sustainability

- Bằng cách sử dụng serverless managed services, Sonar có thể điều chỉnh hạ tầng theo nhu cầu thực tế của khách hàng. Điều này giúp tránh overprovisioning và giảm tác động môi trường của giải pháp

![Ảnh minh hoa](/images/4.jpg)

Link bài viết gốc: https://aws.amazon.com/vi/blogs/architecture/how-sonar-built-a-unified-api-on-aws/