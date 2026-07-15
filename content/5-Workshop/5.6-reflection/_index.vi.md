---
title: "Tổng kết"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Khó khăn
- Cấu hình IAM role cần đúng quyền cho từng dịch vụ.
- API Gateway và Cognito cần khớp JWT authorizer.
- SES sandbox yêu cầu email gửi và nhận phải được xác thực.
- SQS FIFO và DLQ cần cấu hình đúng redrive policy.

### Cách xử lý
- Kiểm tra CloudWatch logs khi Lambda hoặc API lỗi.
- Kiểm tra lại environment variables của Lambda.
- Xác minh SES identity trước khi test gửi email.
- Theo dõi SQS và DLQ để kiểm tra notification flow.

### Hướng phát triển
- Tích hợp Amazon Bedrock hoặc Comprehend để phân loại ticket tự động.
- Bổ sung live chat bằng API Gateway WebSocket.
- Xây dựng dashboard báo cáo bằng Athena hoặc QuickSight.
