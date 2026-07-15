---
title: "Các bước thực hiện"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

Phần này hướng dẫn các bước triển khai chính của AWS Helpdesk Portal.

### 1. Tạo S3 bucket cho frontend

💡 **Information:** S3 frontend bucket dùng để lưu các file giao diện tĩnh của Helpdesk Portal như HTML, CSS, JavaScript và các file build. Trong kiến trúc này, S3 đóng vai trò origin để CloudFront phân phối portal đến người dùng.

Tạo S3 bucket để lưu các file frontend tĩnh.

1. Mở **Amazon S3** và chọn **Create bucket**.
2. Nhập tên bucket, ví dụ `helpdesk-ticket`.
3. Giữ **Block all public access** vì người dùng sẽ truy cập qua CloudFront.
4. Chọn **Create bucket**.

⚠️ **Warning:** Không public frontend bucket nếu bạn dùng CloudFront với Origin Access Control. Người dùng nên truy cập qua CloudFront thay vì truy cập trực tiếp vào S3.

![Tạo S3 frontend bucket](/images/5-Workshop/5.3-Implementation/Frontend/S3/CreateS3.png?width=90pc&classes=shadow)

*Hình 1: Tạo S3 bucket dùng để lưu frontend.*

Upload các file frontend đã build lên bucket.

1. Mở frontend bucket.
2. Chọn **Upload**.
3. Thêm các file và folder frontend.
4. Chọn **Upload**.

![Upload frontend](/images/5-Workshop/5.3-Implementation/Frontend/S3/upload.png?width=90pc&classes=shadow)

*Hình 2: Upload file frontend lên S3 bucket.*

### 2. Tạo OAC và CloudFront distribution

💡 **Information:** CloudFront là điểm truy cập public cho frontend. Dịch vụ này cache nội dung tĩnh tại edge locations, cải thiện tốc độ tải trang và cho phép tích hợp với WAF để bảo vệ lớp web.

Tạo Origin Access Control trước để CloudFront truy cập S3 private bucket an toàn.

1. Mở **Amazon CloudFront**.
2. Vào **Origin access**.
3. Chọn **Create control setting**.
4. Chọn origin type là **S3**.
5. Giữ signing behavior được khuyến nghị.
6. Tạo OAC.

![Tạo origin access](/images/5-Workshop/5.3-Implementation/Frontend/Cloudfront/create-origin-access.png?width=90pc&classes=shadow)

*Hình 3: Cấu hình Origin Access Control cho S3.*

Tạo CloudFront distribution và kết nối với frontend S3 bucket.

1. Chọn **Create distribution**.
2. Ở **Origin domain**, chọn S3 frontend bucket.
3. Chọn OAC đã tạo ở bước trước.
4. Đặt **Default root object** là `index.html`.

![Tạo CloudFront](/images/5-Workshop/5.3-Implementation/Frontend/Cloudfront/createCloudF.png?width=90pc&classes=shadow)

*Hình 4: Tạo CloudFront distribution cho frontend.*

Kiểm tra lại cấu hình chính trước khi tạo distribution.

1. Giữ cache behavior mặc định nếu dự án không yêu cầu cấu hình riêng.
2. Chọn **Create distribution**.
3. Nếu CloudFront hiển thị policy cho OAC, cập nhật policy đó vào S3 bucket.

⚠️ **Warning:** CloudFront cần vài phút để deploy thay đổi. Hãy chờ distribution có trạng thái **Deployed** trước khi kiểm thử URL portal.

![Chọn cấu hình CloudFront](/images/5-Workshop/5.3-Implementation/Frontend/Cloudfront/choose%20setting.png?width=90pc&classes=shadow)

*Hình 5: Kiểm tra cấu hình CloudFront distribution.*

### 3. Gắn AWS WAF vào CloudFront

💡 **Information:** AWS WAF bảo vệ CloudFront endpoint khỏi các rủi ro web phổ biến như SQL Injection, Cross-Site Scripting và request bất thường. Đây là lớp bảo vệ trước khi traffic đi vào frontend.

Tạo WAF Web ACL để bảo vệ endpoint public của CloudFront.

1. Mở **AWS WAF & Shield**.
2. Chọn **Web ACLs > Create web ACL**.
3. Chọn resource type là **CloudFront distributions**.
4. Kiểm tra và tạo Web ACL.

⚠️ **Warning:** Khi bảo vệ CloudFront, WAF scope phải là **CloudFront distributions**. Nếu chọn regional scope, Web ACL sẽ không gắn được với CloudFront.

![Tạo WAF](/images/5-Workshop/5.3-Implementation/Frontend/WAF/createWAF.png?width=90pc&classes=shadow)

*Hình 6: Tạo WAF Web ACL.*

Associate Web ACL với CloudFront distribution.

1. Trong Web ACL, chọn **Select resources to protect**.
2. Thêm CloudFront distribution.
3. Lưu cấu hình association.

![Gắn WAF vào CloudFront](/images/5-Workshop/5.3-Implementation/Frontend/WAF/connect-CLF.png?width=90pc&classes=shadow)

*Hình 7: Gắn WAF với CloudFront.*

### 4. Cấu hình Cognito authentication

💡 **Information:** Cognito quản lý xác thực cho Helpdesk Portal. Dịch vụ này giúp user và IT staff đăng nhập an toàn, đồng thời dùng group để tách quyền requester và admin/staff.

Tạo Cognito User Pool để quản lý đăng nhập.

1. Mở **Amazon Cognito**.
2. Chọn **Create user pool**.
3. Chọn email làm phương thức đăng nhập.
4. Cấu hình password policy và user verification.
5. Tạo user pool.

![Tạo Cognito](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/createCog.png?width=90pc&classes=shadow)

*Hình 8: Tạo Cognito User Pool.*

Cấu hình App Client cho frontend.

1. Mở User Pool đã tạo.
2. Vào **App integration**.
3. Tạo hoặc chỉnh sửa App Client.
4. Dùng cấu hình public client cho web frontend.

![Cấu hình app client](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/edit-appclient.png?width=90pc&classes=shadow)

*Hình 9: Cấu hình Cognito App Client.*

Cấu hình callback và sign-out URL.

1. Thêm CloudFront URL vào callback URL.
2. Thêm CloudFront URL vào sign-out URL.
3. Lưu thay đổi.

⚠️ **Warning:** Callback URL và sign-out URL phải khớp chính xác với frontend URL. Nếu sai URL, Cognito có thể đăng nhập lỗi hoặc redirect sai trang.

![Return URL](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/returnURL.png?width=90pc&classes=shadow)

*Hình 10: Cấu hình callback và sign-out URL.*

Tạo group và user mẫu.

1. Tạo các group như `User` và `Admin`.
2. Dùng group để tách quyền requester và staff.

![Tạo group](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/create-group.png?width=90pc&classes=shadow)

*Hình 11: Tạo group để phân quyền người dùng.*

1. Tạo user kiểm thử.
2. Gán user vào đúng group.
3. Đặt hoặc reset password để test.

![Tạo user](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/create-user.png?width=90pc&classes=shadow)

*Hình 12: Tạo user mẫu để kiểm thử.*

### 5. Tạo DynamoDB tables

💡 **Information:** DynamoDB lưu dữ liệu chính của Helpdesk Portal, bao gồm category, ticket và comment của ticket. DynamoDB phù hợp với kiến trúc serverless vì tự mở rộng và không cần quản lý database server.

Tạo DynamoDB table đầu tiên để lưu helpdesk categories.

1. Mở **Amazon DynamoDB** và chọn **Create table**.
2. Nhập tên table `HelpdeskCategories`.
3. Đặt partition key theo data model của backend.
4. Chọn capacity mode **On-demand**.
5. Tạo table.
6. Bật **Point-in-time recovery** nếu cần.

⚠️ **Warning:** Tên table và key của từng DynamoDB table phải khớp với backend code. Nếu đặt khác, API sẽ lỗi khi đọc hoặc ghi dữ liệu.

![Tạo HelpdeskCategories table](/images/5-Workshop/5.3-Implementation/Backend/DynamoDB/CateDB.png?width=90pc&classes=shadow)

*Hình 13: Tạo DynamoDB table lưu helpdesk categories.*

Tạo DynamoDB table thứ hai để lưu ticket records.

1. Chọn **Create table** lần nữa.
2. Nhập tên table `HelpdeskTickets`.
3. Đặt partition key theo data model của ticket, ví dụ `ticketId`.
4. Chọn capacity mode **On-demand**.
5. Tạo table.

![Tạo HelpdeskTickets table](/images/5-Workshop/5.3-Implementation/Backend/DynamoDB/TicketDB.png?width=90pc&classes=shadow)

*Hình 14: Tạo DynamoDB table lưu helpdesk tickets.*

Tạo DynamoDB table thứ ba để lưu comment của ticket.

1. Chọn **Create table** lần nữa.
2. Nhập tên table `HelpdeskComments`.
3. Đặt partition key theo data model của comment.
4. Chọn capacity mode **On-demand**.
5. Tạo table.

![Tạo HelpdeskComments table](/images/5-Workshop/5.3-Implementation/Backend/DynamoDB/CommentDB.png?width=90pc&classes=shadow)

*Hình 15: Tạo DynamoDB table lưu helpdesk comments.*

### 6. Tạo S3 attachment bucket

💡 **Information:** Attachment bucket lưu các file đi kèm ticket như ảnh lỗi, tài liệu hoặc bằng chứng sự cố. Việc lưu file trong S3 giúp tách dữ liệu lớn khỏi metadata ticket trong DynamoDB.

Tạo S3 bucket private để lưu attachment của ticket.

1. Mở **Amazon S3** và chọn **Create bucket**.
2. Nhập tên attachment bucket `Bucket-ticket-h3vt`.
3. Giữ **Block all public access**.
4. Bật **Bucket versioning**.
5. Tạo bucket.

⚠️ **Warning:** Bucket này nên để private vì attachment có thể chứa thông tin nhạy cảm. Không bật public read access.

![Tạo attachment bucket](/images/5-Workshop/5.3-Implementation/Backend/S3-attachment/createS3-attachment.png?width=90pc&classes=shadow)

*Hình 16: Tạo S3 bucket lưu attachment của ticket.*

### 7. Tạo Lambda

💡 **Information:** Lambda là thành phần backend chính. Function này nhận request từ API Gateway, kiểm tra dữ liệu, tạo hoặc cập nhật ticket, ghi dữ liệu vào DynamoDB, xử lý thông tin attachment và gửi message thông báo vào SQS.

Tạo Lambda function xử lý nghiệp vụ ticket.

1. Mở **AWS Lambda** và chọn **Create function**.
2. Chọn **Author from scratch**.
3. Nhập function name, ví dụ `SubmitTicketFunction`.
4. Chọn runtime phù hợp với backend.

![Tạo submit function](/images/5-Workshop/5.3-Implementation/Backend/Lambda-SubmitFunction/createFunction.png?width=90pc&classes=shadow)

*Hình 17: Tạo Lambda.*

Upload source code backend.

1. Mở Lambda function.
2. Upload backend package hoặc source code.
3. Save hoặc deploy function code.

![Upload backend](/images/5-Workshop/5.3-Implementation/Backend/Lambda-SubmitFunction/uploadbackend.png?width=90pc&classes=shadow)

*Hình 18: Upload backend code lên Lambda.*

Cấu hình environment variables cho backend.

1. Thêm biến cho DynamoDB table name và S3 attachment bucket.
2. Lưu cấu hình.
3. SQS queue URL sẽ được thêm sau khi tạo queue ở bước 9.

![Cấu hình environment](/images/5-Workshop/5.3-Implementation/Backend/Lambda-SubmitFunction/setting%20environment.png?width=90pc&classes=shadow)

*Hình 19: Cấu hình environment variables cho Lambda.*

### 8. Tạo API Gateway

💡 **Information:** API Gateway tạo lớp HTTPS API cho frontend. Dịch vụ này nhận request từ browser, kiểm tra quyền qua Cognito JWT và chuyển request hợp lệ đến Lambda.

Tạo HTTP API và kết nối với Lambda.

1. Mở **Amazon API Gateway**.
2. Chọn **Create API**.
3. Chọn **HTTP API**.
4. Thêm Lambda integration và chọn `SubmitTicketFunction`.

![Tạo HTTP API](/images/5-Workshop/5.3-Implementation/Backend/API/create-HTTP-API.png?width=90pc&classes=shadow)

*Hình 20: Tạo HTTP API.*

Thêm route cho các thao tác ticket.

1. Tạo các route như `ANY /{proxy+}`.
2. Cấu hình Cognito JWT authorizer.
3. Gắn authorizer vào các route cần bảo vệ.
4. Deploy API stage.

⚠️ **Warning:** Không để các route ticket ở trạng thái unauthenticated. Hãy gắn Cognito JWT authorizer cho các route tạo, đọc hoặc cập nhật ticket.

![Thêm route API](/images/5-Workshop/5.3-Implementation/Backend/API/add-route-API.png?width=90pc&classes=shadow)

*Hình 21: Thêm route xử lý ticket.*

Thêm các trigger từ API Gateway sang Lambda.

1. Mở Lambda function.
2. Thêm API Gateway xuất hiện trong phần trigger.
3. Lưu lại API endpoint để cấu hình frontend.

![Thêm Lambda trigger](/images/5-Workshop/5.3-Implementation/Backend/API/add-triggerAPI-forSubFunction.png?width=90pc&classes=shadow)

*Hình 22: Kết nối API Gateway với Lambda.*

### 9. Tạo SQS FIFO queue và DLQ

💡 **Information:** SQS tách luồng xử lý ticket khỏi luồng gửi email. Ticket Handler có thể kết thúc nhanh sau khi đưa message vào queue, còn Notification Worker sẽ xử lý gửi email bất đồng bộ.

Tạo Dead Letter Queue trước.

1. Mở **Amazon SQS**.
2. Chọn **Create queue**.
3. Chọn **FIFO**.
4. Nhập tên DLQ, ví dụ `helpdesk-notification-dlq.fifo`.
5. Tạo queue.

⚠️ **Warning:** Tên FIFO queue bắt buộc kết thúc bằng `.fifo`. Hãy tạo DLQ trước rồi mới cấu hình DLQ cho main queue.

![Tạo DLQ](/images/5-Workshop/5.3-Implementation/Backend/SQS/DLQ/createDLQ.png?width=90pc&classes=shadow)

*Hình 23: Tạo dead-letter queue.*

Tạo main FIFO notification queue.

1. Chọn **Create queue**.
2. Chọn **FIFO**.
3. Nhập tên main queue, ví dụ `helpdesk-notification-queue.fifo`.
4. Bật **Content-based deduplication** nếu phù hợp.

![Tạo SQS queue](/images/5-Workshop/5.3-Implementation/Backend/SQS/Queue/createSQS-queue.png?width=90pc&classes=shadow)

*Hình 24: Tạo main FIFO notification queue.*

Gắn DLQ vào main queue.

1. Trong cấu hình main queue, chọn **Dead-letter queue**.
2. Chọn DLQ.
3. Đặt maximum receive count.
4. Copy main queue URL.
5. Quay lại **SubmitTicketFunction** và thêm/cập nhật environment variable cho SQS queue URL.

⚠️ **Warning:** Nếu không cập nhật SQS queue URL trong environment variables của Lambda, ticket vẫn có thể tạo được nhưng notification message sẽ không được đưa vào queue.

![Gắn DLQ](/images/5-Workshop/5.3-Implementation/Backend/SQS/Queue/add-DLQ.png?width=90pc&classes=shadow)

*Hình 25: Gắn DLQ vào main queue.*

### 10. Tạo Lambda Notification Worker

💡 **Information:** Lambda Notification Worker đọc message từ SQS và gửi email thông báo thông qua Amazon SES. Cách tách riêng này giúp luồng API chính không bị phụ thuộc trực tiếp vào quá trình gửi email và dễ retry khi lỗi.

Tạo Lambda function để gửi email thông báo.

1. Mở **AWS Lambda** và tạo function mới.
2. Nhập function name, ví dụ `NotificationWorkerFunction`.
3. Chọn runtime phù hợp với notification worker code.

![Tạo notification function](/images/5-Workshop/5.3-Implementation/Notification/NotificationFunction/createFunction.png?width=90pc&classes=shadow)

*Hình 26: Tạo Lambda Notification Worker Function.*

Upload notification worker code.

1. Mở tab **Code** của function.
2. Chọn **Upload from** và upload source package của worker.
3. Lưu hoặc deploy phần code vừa upload.

![Upload notification worker code](/images/5-Workshop/5.3-Implementation/Notification/NotificationFunction/upload-file.png?width=90pc&classes=shadow)

*Hình 27: Upload notification worker code lên Lambda.*

Cấu hình environment variables cho notification.

1. Thêm biến sender email và biến liên kết với Cognito.
2. Lưu cấu hình.

⚠️ **Warning:** Nếu SES vẫn ở sandbox mode, cả email gửi và email nhận đều phải được verify thì email mới gửi thành công.

![Thêm environment](/images/5-Workshop/5.3-Implementation/Notification/NotificationFunction/add-environment.png?width=90pc&classes=shadow)

*Hình 28: Cấu hình environment variables cho notification Lambda.*

Thêm SQS trigger.

1. Chọn **Add trigger**.
2. Chọn **SQS**.
3. Chọn main FIFO notification queue.
4. Lưu trigger.

![Thêm SQS trigger](/images/5-Workshop/5.3-Implementation/Notification/NotificationFunction/add-trigger-SQS.png?width=90pc&classes=shadow)

*Hình 29: Thêm SQS làm trigger cho Lambda.*

### 11. Cấu hình CloudWatch và SNS monitoring

💡 **Information:** CloudWatch thu thập logs và metrics từ Lambda, SQS, còn SNS gửi cảnh báo cho nhóm vận hành. Nhờ đó nhóm có thể phát hiện lỗi backend, lỗi queue hoặc lỗi notification nhanh hơn.

Theo dõi Lambda bằng CloudWatch.

1. Mở **Amazon CloudWatch**.
2. Kiểm tra Lambda log groups và metrics.
3. Tạo alarm cho Lambda errors.

![CloudWatch Lambda](/images/5-Workshop/5.3-Implementation/Monitoring/CloudWatch/CloudWatch-Lambda.png?width=90pc&classes=shadow)

*Hình 30: Theo dõi Lambda metrics trong CloudWatch.*

Theo dõi SQS và cấu hình SNS notification.

1. Kiểm tra metrics của main queue và DLQ.
2. Tạo alarm cho message trong DLQ.

⚠️ **Warning:** Nếu DLQ có visible message, nghĩa là notification flow đã lỗi sau nhiều lần retry. Cần kiểm tra DLQ trong bước validation.

![CloudWatch SQS](/images/5-Workshop/5.3-Implementation/Monitoring/CloudWatch/CloudWatch-SQS.png?width=90pc&classes=shadow)

*Hình 31: Theo dõi SQS metrics trong CloudWatch.*

Theo dõi Lambda notification bằng CloudWatch.

1. Kiểm tra Lambda notification log groups và metrics.
2. Tạo alarm cho Lambda notification.


![Alarm notification](/images/5-Workshop/5.3-Implementation/Monitoring/CloudWatch/CloudWatch-notification.png?width=90pc&classes=shadow)

*Hình 32: Cấu hình alarm notification cho CloudWatch.*

Cấu hình SNS subscription để nhận email cảnh báo từ CloudWatch Alarm.

1. Mở **Amazon SNS** và chọn alert topic.
2. Chọn SNS topic làm nơi nhận alarm notification.
3. Kiểm tra subscription protocol là **Email**.
4. Xác nhận email subscription.
5. Đảm bảo subscription status là **Confirmed**.

![Cấu hình SNS](/images/5-Workshop/5.3-Implementation/Monitoring/SNS/settingSNS.png?width=90pc&classes=shadow)

*Hình 33: Cấu hình SNS subscription để nhận cảnh báo.*

### 12. Tạo CI/CD pipeline

💡 **Information:** CI/CD pipeline tự động hóa quá trình upload source, build và deploy. Điều này giảm lỗi thao tác thủ công và giúp frontend/backend được triển khai theo cùng một quy trình.

Tạo source repository.

1. Mở **AWS CodeCommit**.
2. Tạo repository.
3. Upload hoặc push source code của dự án.

![CodeCommit upload](/images/5-Workshop/5.3-Implementation/CI-CD/CodeCommit/create-upload.png?width=90pc&classes=shadow)

*Hình 34: Tạo repository và upload source code.*

Tạo build project.

1. Mở **AWS CodeBuild**.
2. Tạo 2 build project cho frontend và backend.
3. Chọn CodeCommit repository làm source.

⚠️ **Warning:** CodeBuild service role cần quyền cập nhật S3 và Lambda. Nếu thiếu quyền, build có thể chạy nhưng deploy sẽ thất bại.

![Tạo CodeBuild](/images/5-Workshop/5.3-Implementation/CI-CD/CodeBuild/createCodeBuild.png?width=90pc&classes=shadow)

*Hình 35: Tạo CodeBuild project.*

Tạo và chạy pipeline.

1. Mở **AWS CodePipeline**.
2. Tạo pipeline với CodeCommit là Source và CodeBuild là Build.
3. Chạy pipeline `Release change` và xác nhận build thành công.

![Kiểm thử CodePipeline](/images/5-Workshop/5.3-Implementation/CI-CD/Codepipeline/createCodepipeline-test.png?width=90pc&classes=shadow)

*Hình 36: Cấu hình và chạy CI/CD pipeline.*

### 13. Cấu hình KMS và AWS Backup

💡 **Information:** KMS bảo vệ dữ liệu lưu trữ bằng encryption key, còn AWS Backup chuẩn bị khả năng khôi phục cho tài nguyên quan trọng. Hai thành phần này giúp tăng bảo mật và giảm rủi ro mất dữ liệu.

Tạo KMS key để mã hóa dữ liệu.

1. Mở **AWS KMS**.
2. Chọn **Customer managed keys**.
3. Tạo key cho dự án.

![Tạo KMS](/images/5-Workshop/5.3-Implementation/secure/KMS/createKMS.png?width=90pc&classes=shadow)

*Hình 37: Tạo KMS key để mã hóa dữ liệu.*

Áp dụng KMS encryption cho tài nguyên.

1. Áp dụng key cho DynamoDB nếu cần.
2. Áp dụng key cho S3 attachment bucket.

⚠️ **Warning:** Không schedule deletion KMS key nếu key vẫn đang được S3, DynamoDB hoặc backup sử dụng. Dữ liệu đã mã hóa bằng key đó có thể không truy cập được.

![Cấu hình KMS](/images/5-Workshop/5.3-Implementation/secure/KMS/setKMS.png?width=90pc&classes=shadow)

*Hình 38: Áp dụng KMS encryption cho tài nguyên của dự án.*

Tạo tài nguyên AWS Backup.

1. Mở **AWS Backup**.
2. Tạo backup vault cho dự án.

![Tạo backup vault](/images/5-Workshop/5.3-Implementation/secure/Backup/createBackup-vault.png?width=90pc&classes=shadow)

*Hình 39: Tạo AWS Backup vault.*

1. Tạo Backup plan.
2. Cấu hình lịch backup và thời gian lưu trữ.

![Tạo backup plan](/images/5-Workshop/5.3-Implementation/secure/Backup/createBackup-plan.png?width=90pc&classes=shadow)

*Hình 40: Tạo AWS Backup plan.*

1. Gắn Backup vault vào Backup plan.
2. Lưu backup selection.

![Gắn resource backup](/images/5-Workshop/5.3-Implementation/secure/Backup/connect-vault-plan.png?width=90pc&classes=shadow)

*Hình 41: Gắn liên kết Backup vault vào Backup plan.*
