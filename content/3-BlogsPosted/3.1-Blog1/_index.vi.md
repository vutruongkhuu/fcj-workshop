---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Tối Ưu Khả Năng Phục Hồi Ứng Dụng Toàn Cầu Với AWS Global Accelerator

Bạn đã bao giờ gặp tình huống hệ thống của mình bị sập ở một khu vực (Region), hoặc người dùng ở xa than phiền vì ứng dụng quay "vòng vòng" do độ trễ mạng quá cao?

Cách giải quyết thông thường là sử dụng DNS truyền thống (như Route 53) để định tuyến. Tuy nhiên, DNS thường bị ảnh hưởng bởi cơ chế bộ nhớ đệm (TTL Caching) của các nhà mạng, khiến việc chuyển vùng khi có sự cố (Failover) bị chậm trễ từ vài phút đến vài giờ.

Trong bài viết này, mình sẽ chia sẻ cho các bạn một kiến trúc nâng cao: "AWS Global Accelerator" – giải pháp không chỉ tối ưu hiệu năng mạng mà còn giúp hệ thống tự động phục hồi (Failover) gần như ngay lập tức dựa trên mạng lưới hạ tầng toàn cầu của AWS.

Cái Nhìn Tổng Quan Về Kiến Trúc:

Sơ đồ dưới đây mô tả một hệ thống đa vùng (Multi-Region) hoàn chỉnh được bảo vệ và tối ưu hóa thông qua AWS Global Accelerator.

Hệ thống sử dụng hai địa chỉ IP tĩnh toàn cầu (Static Anycast IPs) làm đầu mối duy nhất cho người dùng cuối. Đằng sau đó là mạng lưới các điểm chuyển tiếp (Edge Locations) kết nối trực tiếp vào đường truyền backbone tốc độ cao của AWS, dẫn về các tài nguyên đích (Application Load Balancers - ALB hoặc EC2) đặt tại nhiều AWS Regions khác nhau.

**AWS Global Accelerator Hoạt Động Như Thế Nào?**

- Địa chỉ IP tĩnh toàn cầu (Anycast IP): Global Accelerator cung cấp cho bạn hai địa chỉ IP tĩnh cố định. Dù ứng dụng của bạn có mở rộng thêm vùng hay thay đổi hạ tầng bên dưới, IP này vẫn giữ nguyên, giúp đơn giản hóa việc cấu hình tường lửa (Firewall) phía khách hàng.
- Định tuyến qua mạng nội bộ AWS: Thay vì để lưu lượng truy cập của người dùng "đi lòng vòng" trên mạng Internet công cộng đầy bất ổn, Global Accelerator sẽ đón dòng tiền lưu lượng ngay tại Edge Location gần user nhất và đưa nó vào mạng backbone riêng, bảo mật và siêu tốc của AWS.

**Cơ Chế Dynamic Health Checks & Failover Hoạt Động Ra Sao?**

Thông thường, khi một vùng gặp sự cố, các hệ thống DNS phải mất thời gian để cập nhật. AWS Global Accelerator giải quyết bài toán này một cách "thông minh" và chủ động:
- Liên tục giám sát (Health Checks): Global Accelerator liên tục kiểm tra trạng thái sức khỏe của các ứng dụng (ALB/EC2) ở các Region.
- Chuyển vùng tức thì (Instant Failover): Nếu phát hiện ứng dụng tại Region A gặp sự cố (Unhealthy), Global Accelerator sẽ ngay lập tức cô lập vùng đó và điều hướng 100% lưu lượng sang Region B còn khỏe mạnh. Quá trình này diễn ra chỉ trong vòng "vài giây", người dùng cuối hầu như không nhận ra sự gián đoạn.
- Điều phối tải linh hoạt (Traffic Dials): Bạn có thể dễ dàng cấu hình tỷ lệ phần trăm lưu lượng (ví dụ: 80% vào Region chính, 20% vào Region phụ) để phục vụ cho các chiến lược triển khai Blue/Green hoặc điều phối tải tùy theo nhu cầu.

**Người Dùng Cuối (End User) Được Hưởng Lợi Gì?**

- Trải nghiệm mượt mà, không gián đoạn: Nhờ cơ chế failover thần tốc, ứng dụng của bạn đạt mức độ sẵn sàng cao (High Availability), giảm thiểu tối đa thời gian chết (Downtime).
- Tốc độ truy cập vượt trội: Do được "đi đường tắt" bằng mạng backbone của AWS, độ trễ (Latency) giảm đáng kể, hiện tượng mất gói tin (Packet loss) và giật lag được loại bỏ, đặc biệt là đối với người dùng ở các quốc gia cách xa máy chủ gốc.

Hệ thống hoạt động theo một quy trình khép kín và tự động hóa hoàn toàn:

1. Người dùng cuối gửi yêu cầu đến ứng dụng thông qua "Anycast IP".
2. "AWS Edge Location" gần nhất tiếp nhận yêu cầu và kiểm tra bảng trạng thái định tuyến.
3. Nếu tất cả các Region đều khỏe mạnh, hệ thống tự động tối ưu hóa đường đi để dẫn người dùng đến "Region có độ trễ thấp nhất".
4. Khi một Region xảy ra sự cố, hệ thống "Health Check" phát hiện và ra lệnh cô lập Region đó ngay lập tức.
5. Dòng lưu lượng được "tự động chuyển hướng" mượt mà sang Region dự phòng an toàn.

![Ảnh minh hoa](/images/1.jpg)
![Ảnh minh hoa](/images/2.jpg)

link bài viết gốc: https://aws.amazon.com/vi/blogs/networking-and-content-delivery/maximising-application-resiliency-with-aws-global-accelerator/?content_source=fb&fb_content_id=Q9-wBQFdqBQ2SBnCPxy6A0UN5A51W2ePxGx8rDCk2KPqZwkXKhi67tpncnus-xObJQ&channel_type=fb&fbclid=IwY2xjawSrSFZleHRuA2FlbQIxMABicmlkETFFWXFVbkdQZTh3RGhDSFVMc3J0YwZhcHBfaWQQMjIyMDM5MTc4ODIwMDg5MgABHoi4NP_4y3nSsY2mkdcr8mqDzbJXEJuYTDD6qfaaVhjetmQAjSNRpd1x25I-_aem_d3GeCL--cqIUwpbAwS3pEw