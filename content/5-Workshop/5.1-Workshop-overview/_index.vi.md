---
title: "Giới thiệu tổng quan"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### Mục tiêu
Giới thiệu kiến trúc tổng thể của AWS Helpdesk Portal và các dịch vụ AWS được sử dụng trong hệ thống.

### Kiến trúc tổng thể
Người dùng truy cập portal thông qua CloudFront. Frontend được lưu trên S3 và bảo vệ bằng WAF. Cognito xác thực người dùng, API Gateway kiểm tra JWT token, Lambda xử lý nghiệp vụ ticket. DynamoDB lưu dữ liệu ticket, S3 lưu tệp đính kèm, SQS FIFO xử lý message thông báo, SES gửi email và DLQ lưu các message lỗi. CloudWatch và SNS hỗ trợ giám sát, còn KMS và AWS Backup tăng cường bảo mật và khả năng khôi phục.

![Kiến trúc AWS Helpdesk Portal](/images/5-Workshop/5.1-Workshop-overview/architecture.jpg?width=90pc&classes=shadow)

*Hình 1: Kiến trúc tổng thể của AWS Helpdesk Portal.*
