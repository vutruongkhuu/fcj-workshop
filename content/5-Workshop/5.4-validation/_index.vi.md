---
title: "Kiểm tra kết quả"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Mục tiêu
Kiểm tra luồng hoạt động end-to-end của AWS Helpdesk Portal.

### Kịch bản kiểm thử
1. Đăng nhập bằng Cognito.
2. Tạo ticket mới từ portal.
3. Kiểm tra ticket đã được lưu trong DynamoDB.
4. Kiểm tra message thông báo đã được gửi vào SQS.
5. Kiểm tra Notification Worker gửi email thông qua SES.
6. Kiểm tra logs và metrics trên CloudWatch.
7. Xác nhận message lỗi được chuyển vào DLQ.


### Kết quả mong đợi
Portal có thể tạo ticket, lưu dữ liệu, gửi thông báo, giám sát hoạt động và xử lý message lỗi an toàn thông qua DLQ.

### Video demo
https://drive.google.com/file/d/16sIRaHBujSmE8vAJL9YL83sB_UAVMmpr/view?usp=drive_link