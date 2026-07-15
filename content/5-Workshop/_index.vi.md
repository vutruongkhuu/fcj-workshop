---
title: "Workshop"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Workshop AWS Helpdesk Portal

#### Tổng quan

Trong workshop này, bạn sẽ xây dựng thủ công **AWS Helpdesk Portal**, một hệ thống quản lý yêu cầu hỗ trợ CNTT theo kiến trúc serverless trên AWS. Người dùng có thể đăng nhập, tạo ticket, tải tệp đính kèm, theo dõi trạng thái xử lý và nhận email thông báo. Nhân viên IT có thể xem, phân công, cập nhật và hoàn tất ticket trên cùng một cổng quản trị.

Workshop bám theo kiến trúc nhóm đã trình bày:

+ **Frontend:** Amazon S3, Amazon CloudFront, AWS WAF
+ **Authentication:** Amazon Cognito và JWT authorization thông qua Amazon API Gateway
+ **Backend:** Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon S3
+ **Security and resilience:** AWS KMS, AWS Backup
+ **Notification:** Amazon SQS FIFO, SQS DLQ, AWS Lambda Notification Worker và Amazon SES
+ **Monitoring:** Amazon CloudWatch và Amazon SNS
+ **CI/CD:** AWS CodeCommit, AWS CodeBuild và AWS CodePipeline

#### Nội dung

1. [Giới thiệu tổng quan](5.1-workshop-overview/)
2. [Các bước chuẩn bị](5.2-prerequisites/)
3. [Các bước thực hiện](5.3-implementation-steps/)
4. [Kiểm tra kết quả thực nghiệm](5.4-validation/)
5. [Dọn dẹp tài nguyên](5.5-cleanup/)
6. [Thách thức & Hướng phát triển](5.6-reflection/)
