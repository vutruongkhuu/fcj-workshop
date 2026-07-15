---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

Tại phần này, bạn cần tóm tắt các nội dung trong workshop mà bạn **dự tính** sẽ làm.

# BÁO CÁO TÓM TẮT DỰ ÁN: AWS HELPDESK PORTAL
### **Kiến trúc hệ thống:** Serverless (Không máy chủ) trên nền tảng Amazon Web Services (AWS)

---

### 1. Tóm tắt điều hành
Dự án **AWS Helpdesk Portal** là một hệ thống quản lý yêu cầu hỗ trợ công nghệ thông tin (IT Helpdesk) toàn diện, được thiết kế và triển khai hoàn toàn trên nền tảng điện toán đám mây AWS theo mô hình kiến trúc **Serverless**. Mục tiêu cốt lõi của hệ thống là tự động hóa quy trình tiếp nhận, phân loại, xử lý sự cố CNTT nội bộ và tối ưu hóa luồng tương tác giữa người dùng cuối (User) và đội ngũ kỹ thuật (Staff). Nhờ tận dụng tối đa các dịch vụ managed-services từ AWS, hệ thống đạt khả năng co giãn tự động linh hoạt, đảm bảo tính sẵn sàng cao, bảo mật nghiêm ngặt và tối ưu hóa tối đa chi phí vận hành (chỉ tính tiền khi có request xử lý).

---

### 2. Tuyên bố vấn đề
Các hệ thống quản lý yêu cầu hỗ trợ truyền thống tại nhiều tổ chức hiện nay thường gặp phải những bất cập lớn:
* **Quy trình rời rạc, thủ công:** Yêu cầu gửi qua nhiều kênh (Email, Chat, truyền miệng) dễ bị thất lạc, thiếu sự đồng bộ và khó theo dõi trạng thái.
* **Chi phí hạ tầng cố định cao:** Việc duy trì hệ thống máy chủ chạy 24/7 gây lãng phí tài nguyên lớn trong các khung giờ thấp điểm hoặc ngày nghỉ.
* **Kém linh hoạt khi tải tăng đột biến:** Hệ thống dễ bị nghẽn mạch hoặc quá tải khi có sự cố hệ thống diện rộng khiến hàng loạt Ticket đổ về cùng lúc.
* **Nguy cơ bảo mật thông tin:** Dữ liệu sự cố nội bộ hoặc thông tin cá nhân của nhân sự không được mã hóa đúng chuẩn dễ dẫn đến rò rỉ dữ liệu.

---

### 3. Kiến trúc giải pháp
Hệ thống được thiết kế theo kiến trúc hướng sự kiện (Event-Driven Architecture) chia làm các phân tầng clear-cut:
1. **Tầng Giao diện (Frontend & Edge Processing):** Mã nguồn tĩnh (HTML/ReactJS) lưu trữ an toàn trong **Amazon S3** và phân phối qua mạng lưới Edge Location của **Amazon CloudFront** để tối ưu tốc độ phản hồi toàn cầu. Toàn bộ traffic đi qua **AWS WAF** nhằm ngăn chặn các cuộc tấn công phổ biến (SQL Injection, XSS).
2. **Tầng Xác thực (Authentication):** Sử dụng **Amazon Cognito** để quản lý danh tính người dùng và cấp chứng chỉ số JWT Token sau khi đăng nhập thành công.
3. **Tầng Nghiệp vụ (Compute & API):** **Amazon API Gateway** đóng vai trò cửa ngõ tiếp nhận API Call, kiểm tra tính hợp lệ của JWT Token và điều hướng trực tiếp đến **AWS Lambda (Ticket Handler)** để xử lý logic nghiệp vụ xử lý Ticket.
4. **Tầng Lưu trữ (Data & Storage):** Thông tin thuộc tính của Ticket (ID, trạng thái, mô tả) lưu tại cơ sở dữ liệu NoSQL **Amazon DynamoDB**. Các file ảnh lỗi, tài liệu đính kèm được đẩy thẳng vào **Amazon S3 (Attachment Storage)**.

---

### 4. Triển khai kỹ thuật
* **An ninh dữ liệu:** Toàn bộ dữ liệu lưu trữ (Data-at-rest) trên DynamoDB và S3 đều được mã hóa tự động bằng khóa quản lý trong **AWS KMS**. Kích hoạt tính năng **S3 Versioning** để chống ghi đè/xóa nhầm dữ liệu.
* **Độ tin cậy & Dự phòng:** Cấu hình **Amazon SQS FIFO** bảo đảm tính toàn vẹn và thứ tự trước-sau của các thông báo sự kiện, kết hợp với **SQS Dead Letter Queue (DLQ)** để hứng và cô lập các message bị lỗi để đội ngũ Dev kiểm tra. Bật tính năng **Point-in-Time Recovery (PITR)** của DynamoDB kết hợp với **AWS Backup** giúp khôi phục hệ thống về bất kỳ thời điểm nào khi có sự cố thảm họa dữ liệu.
* **Giám sát thông minh:** Hệ thống sử dụng **Amazon CloudWatch** để theo dõi hiệu năng và thu thập logs tập trung từ các hàm Lambda. Khi các chỉ số vượt ngưỡng cảnh báo, **Amazon SNS** sẽ kích hoạt quy trình gửi cảnh báo (Alert Notification) trực tiếp tới các kỹ sư DevOps.
* **Tự động hóa CI/CD:** Chu trình phát triển phần mềm được quản lý khép kín bằng **AWS CodeCommit**, biên dịch tự động qua **AWS CodeBuild** và phân phối triển khai tự động bằng **AWS CodePipeline** lên S3 và Lambda, giảm thiểu tối đa sai sót từ con người.

---

### 5. Ước tính ngân sách
Nhờ đặc thù của kiến trúc Serverless (Pay-as-you-go), chi phí phát sinh tỷ lệ thuận với số lượng Request thực tế. Dưới đây là bảng tính toán chi phí ước tính cho quy mô **50,000 Tickets/tháng** (tương đương khoảng 1,000,000 API requests):

| Dịch vụ AWS | Thành phần tính phí chính | Chi phí hàng tháng (Ước tính) |
| :--- | :--- | :---: |
| **AWS Lambda** | ~500,000 executions (Handler + Worker), thời gian chạy ~200ms | $1.00 |
| **Amazon API Gateway** | ~1,000,000 REST requests | $3.50 |
| **Amazon DynamoDB** | 10 GB dữ liệu tĩnh + Đọc/Ghi dữ liệu + PITR | $3.50 |
| **Amazon S3** | 50 GB dữ liệu đính kèm và lưu trữ Frontend | $1.25 |
| **Amazon CloudFront & WAF** | Băng thông truyền tải (Data Transfer Out) + WAF Web ACL Rules | $12.00 |
| **Các dịch vụ khác** | AWS KMS, AWS Backup, Amazon SQS, CloudWatch, SES (gửi email) | $5.00 |
| **TỔNG CỘNG** | **Chi phí vận hành hệ thống lý tưởng hàng tháng** | **~$26.25 / tháng** |

---

### 6. Đánh giá rủi ro
> 💡 **Rủi ro 1: Hiện tượng Cold Start của AWS Lambda**
> * **Mô tả:** Khi hệ thống không có request trong thời gian dài, lần gọi hàm Lambda đầu tiên sẽ mất 1-3 giây để khởi tạo môi trường, gây trễ cho người dùng.
> * **Biện pháp giảm thiểu:** Tối ưu hóa dung lượng gói code, sử dụng ngôn ngữ khởi động nhanh (Node.js) và áp dụng cấu hình *Provisioned Concurrency* cho các API Core nếu cần thiết.

> 💡 **Rủi ro 2: Chi phí tăng vọt ngoài tầm kiểm soát (Billing Spikes)**
> * **Mô tả:** Cơ chế tự động co giãn vô hạn của Serverless có thể bị khai thác nếu hệ thống dính vòng lặp code vô chậm hoặc bị tấn công dồn dập, dẫn đến hóa đơn AWS tăng đột biến.
> * **Biện pháp giảm thiểu:** Thiết lập cấu hình *Rate Limit* trên API Gateway, giới hạn mức thực thi đồng thời (Concurrency Limit) của Lambda và cài đặt hạn mức cảnh báo chi phí bằng **AWS Budgets** (gửi mail cảnh báo ngay khi chi phí chạm ngưỡng $30).

---

### 7. Kết quả mong đợi
* **Giảm 40% thời gian xử lý sự cố (MTTR):** Việc tự động hóa luồng phân phối qua hàng đợi SQS và thông báo SES giúp các kỹ sư công nghệ tiếp cận và giải quyết Ticket nhanh chóng hơn.
* **Tiết kiệm hơn 70% chi phí hạ tầng:** Loại bỏ hoàn toàn chi phí lãng phí của các máy chủ ảo chạy không tải so với kiến trúc truyền thống.
* **Khả năng chịu lỗi và tính sẵn sàng cao (High Availability):** Hệ thống tự động phân tán đa vùng (Multi-AZs) theo cơ chế mặc định của AWS Serverless, đảm bảo cổng thông tin hoạt động ổn định 99.99%.