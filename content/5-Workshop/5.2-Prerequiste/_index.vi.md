---
title: "Các bước chuẩn bị"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Mục tiêu
Chuẩn bị IAM roles, email xác thực và kênh cảnh báo trước khi triển khai hệ thống.

### 1. Kiểm tra IAM roles
Vào **IAM > Roles** và kiểm tra các role dùng cho Lambda, CodeBuild, CodePipeline và AWS Backup. Các role này giúp từng dịch vụ có quyền phù hợp để thực hiện nhiệm vụ.

![IAM roles](/images/5-Workshop/5.2-Prerequisites/IAM/roleIAM.png?width=90pc&classes=shadow)

*Hình 1: Các IAM role được sử dụng bởi Lambda, CodeBuild, CodePipeline và AWS Backup.*

### 2. Xác thực email SES
Vào **Amazon SES > Verified identities** và xác thực email dùng để gửi/nhận thông báo ticket.

![SES identity](/images/5-Workshop/5.2-Prerequisites/SES/SES.png?width=90pc&classes=shadow)

*Hình 2: Email identity đã được xác thực trong Amazon SES.*

### 3. Tạo SNS topic
Vào **Amazon SNS** và tạo topic/subscription để nhận cảnh báo từ CloudWatch Alarm.

![SNS topic](/images/5-Workshop/5.2-Prerequisites/SNS/SNS.png?width=90pc&classes=shadow)

*Hình 3: SNS topic dùng để nhận cảnh báo vận hành.*
