---
title: "Dọn dẹp tài nguyên"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Mục tiêu
Xóa toàn bộ tài nguyên AWS đã tạo trong workshop để tránh phát sinh chi phí ngoài ý muốn.

### 1. Xóa tài nguyên CI/CD
Xóa **CodePipeline** trước, sau đó xóa **CodeBuild** project và **CodeCommit** repository của dự án.

1. Mở **AWS CodePipeline**.
2. Chọn pipeline của Helpdesk Portal.
3. Chọn **Delete pipeline** và xác nhận.
4. Mở **AWS CodeBuild**.
5. Xóa các build project của frontend/backend.
6. Mở **AWS CodeCommit**.
7. Xóa repository của dự án.

### 2. Xóa tài nguyên monitoring
Xóa CloudWatch alarms, dashboard và log groups đã tạo cho workshop.

1. Mở **Amazon CloudWatch**.
2. Vào **Alarms** và xóa các alarm liên quan đến Lambda, SQS DLQ hoặc API Gateway.
3. Vào **Dashboards** và xóa dashboard của dự án.
4. Vào **Log groups**.
5. Xóa log groups của Lambda và CodeBuild nếu không còn sử dụng.

### 3. Xóa SNS resources
Xóa SNS topic và subscriptions dùng để nhận cảnh báo.

1. Mở **Amazon SNS**.
2. Vào **Topics**.
3. Chọn topic cảnh báo của dự án và chọn **Delete**.
4. Vào **Subscriptions**.
5. Xóa các subscription còn lại liên quan đến topic.

### 4. Xóa API Gateway
Xóa API endpoint public mà frontend sử dụng.

1. Mở **Amazon API Gateway**.
2. Chọn **APIs**.
3. Chọn API của Helpdesk Portal.
4. Chọn **Delete** và xác nhận.

### 5. Xóa Lambda functions
Cần xóa SQS trigger trước khi xóa Lambda Notification Worker.

1. Mở **AWS Lambda**.
2. Chọn function **Notification Worker**.
3. Vào **Configuration > Triggers**.
4. Xóa SQS trigger.
5. Quay lại danh sách Lambda functions.
6. Xóa các Lambda function của dự án, ví dụ:
   - `SubmitTicketFunction`
   - `NotificationFunction`

### 6. Xóa SQS queues
Xóa main FIFO queue và Dead Letter Queue.

1. Mở **Amazon SQS**.
2. Chọn notification FIFO queue.
3. Chọn **Delete** và xác nhận.
4. Chọn DLQ.
5. Chọn **Delete** và xác nhận.

### 7. Xóa DynamoDB table
Xóa table dùng để lưu dữ liệu ticket.

1. Mở **Amazon DynamoDB**.
2. Chọn **Tables**.
3. Chọn ticket table.
4. Chọn **Delete table**.
5. Xác nhận xóa.

### 8. Xóa S3 buckets
Cần empty từng bucket trước khi xóa bucket.

1. Mở **Amazon S3**.
2. Chọn frontend bucket.
3. Chọn **Empty** và xác nhận.
4. Xóa frontend bucket.
5. Chọn attachment bucket.
6. Chọn **Empty** và xác nhận.
7. Nếu bucket bật versioning, cần xóa toàn bộ object versions.
8. Xóa attachment bucket.

### 9. Xóa CloudFront distribution
CloudFront distribution cần được disable trước khi xóa.

1. Mở **Amazon CloudFront**.
2. Chọn distribution của frontend.
3. Chọn **Disable**.
4. Chờ trạng thái chuyển sang **Disabled**.
5. Chọn lại distribution.
6. Chọn **Delete** và xác nhận.

### 10. Xóa WAF Web ACL
Xóa WAF Web ACL sau khi không còn gắn với CloudFront.

1. Mở **AWS WAF & Shield**.
2. Chọn **Web ACLs**.
3. Chọn Web ACL dùng cho CloudFront.
4. Kiểm tra Web ACL không còn associate với CloudFront distribution.
5. Chọn **Delete** và xác nhận.

### 11. Xóa Cognito User Pool
Xóa Cognito User Pool dùng cho xác thực người dùng.

1. Mở **Amazon Cognito**.
2. Chọn **User pools**.
3. Chọn User Pool của Helpdesk Portal.
4. Chọn **Delete user pool**.
5. Nhập nội dung xác nhận theo yêu cầu.
6. Chọn **Delete**.

### 12. Xóa SES identities
Chỉ xóa SES identities nếu chúng được tạo riêng cho workshop này.

1. Mở **Amazon SES**.
2. Chọn **Verified identities**.
3. Chọn email identities dùng trong dự án.
4. Chọn **Delete** và xác nhận.

### 13. Xóa AWS Backup resources
Xóa backup plan trước, sau đó xóa backup vault.

1. Mở **AWS Backup**.
2. Chọn **Backup plans**.
3. Chọn backup plan của dự án và xóa.
4. Vào **Backup vaults**.
5. Chọn backup vault của dự án.
6. Nếu vault còn recovery points, hãy xóa recovery points trước.
7. Xóa backup vault.

### 14. Xóa KMS key
KMS key không thể xóa ngay lập tức. Cần schedule key deletion.

1. Mở **AWS KMS**.
2. Chọn **Customer managed keys**.
3. Chọn KMS key đã tạo cho dự án.
4. Chọn **Key actions > Schedule key deletion**.
5. Chọn thời gian chờ, ví dụ **7 days**.
6. Xác nhận lịch xóa key.

### 15. Xóa IAM roles
Chỉ xóa các custom role được tạo riêng cho dự án.

1. Mở **IAM**.
2. Chọn **Roles**.
3. Tìm các role của dự án, ví dụ:
   - Lambda execution role
   - CodeBuild role
   - CodePipeline role
   - Backup role
4. Mở từng role.
5. Detach policy nếu được yêu cầu.
6. Chọn **Delete role**.

{{% notice warning %}}
Không xóa AWS service-linked roles nếu chưa chắc chắn chúng không được tài nguyên khác sử dụng.
{{% /notice %}}

### 16. Kiểm tra cuối cùng
Tìm project name trong các dịch vụ sau và xác nhận không còn tài nguyên active:

- S3
- Lambda
- DynamoDB
- API Gateway
- SQS
- CloudFront
- CloudWatch
- CodePipeline

Cuối cùng, kiểm tra **Billing > Cost Explorer** sau vài giờ để đảm bảo không còn chi phí phát sinh ngoài ý muốn.

### Kết quả mong đợi
Toàn bộ tài nguyên của AWS Helpdesk Portal được xóa thành công. AWS account không còn tài nguyên active liên quan đến workshop.
