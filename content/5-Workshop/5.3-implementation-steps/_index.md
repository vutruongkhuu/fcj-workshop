---
title: "Implementation steps"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

This section guides you through the main implementation steps for AWS Helpdesk Portal.

### 1. Create the frontend S3 bucket

💡 **Information:** The frontend S3 bucket stores the static files of the Helpdesk Portal, such as HTML, CSS, JavaScript, and frontend build assets. In this architecture, S3 acts as the origin that CloudFront uses to deliver the portal to users.

Create an S3 bucket to store the static frontend files.

1. Open **Amazon S3** and choose **Create bucket**.
2. Enter the bucket name, for example `helpdesk-ticket`.
3. Keep **Block all public access** enabled because users will access the site through CloudFront.
4. Choose **Create bucket**.

⚠️ **Warning:** Do not make the frontend bucket public if you use CloudFront with Origin Access Control. Public access should be handled through CloudFront, not directly from S3.

![Create frontend S3 bucket](/images/5-Workshop/5.3-Implementation/Frontend/S3/CreateS3.png?width=90pc&classes=shadow)

*Figure 1: Create the S3 bucket for frontend hosting.*

Upload the frontend build files to the bucket.

1. Open the frontend bucket.
2. Choose **Upload**.
3. Add the frontend files and folders.
4. Choose **Upload**.

![Upload frontend files](/images/5-Workshop/5.3-Implementation/Frontend/S3/upload.png?width=90pc&classes=shadow)

*Figure 2: Upload frontend files to the S3 bucket.*

### 2. Create OAC and CloudFront distribution

💡 **Information:** CloudFront is used as the public entry point for the frontend. It caches static content at edge locations, improves loading speed, and allows the application to integrate with WAF for web protection.

Create an Origin Access Control first so CloudFront can securely access the private S3 bucket.

1. Open **Amazon CloudFront**.
2. Go to **Origin access**.
3. Choose **Create control setting**.
4. Select **S3** as the origin type.
5. Keep the recommended signing behavior.
6. Create the OAC.

![Create origin access](/images/5-Workshop/5.3-Implementation/Frontend/Cloudfront/create-origin-access.png?width=90pc&classes=shadow)

*Figure 3: Configure Origin Access Control for S3.*

Create a CloudFront distribution and connect it to the frontend S3 bucket.

1. Choose **Create distribution**.
2. In **Origin domain**, select the frontend S3 bucket.
3. Select the OAC created in the previous step.
4. Set **Default root object** to `index.html`.

![Create CloudFront](/images/5-Workshop/5.3-Implementation/Frontend/Cloudfront/createCloudF.png?width=90pc&classes=shadow)

*Figure 4: Create a CloudFront distribution for the frontend.*

Review the main distribution settings before creating it.

1. Keep default cache behavior unless the project requires custom settings.
2. Choose **Create distribution**.
3. After creation, update the S3 bucket policy if CloudFront provides the OAC policy notice.

⚠️ **Warning:** CloudFront changes may take several minutes to deploy. Wait until the distribution status is **Deployed** before testing the portal URL.

![Choose CloudFront settings](/images/5-Workshop/5.3-Implementation/Frontend/Cloudfront/choose%20setting.png?width=90pc&classes=shadow)

*Figure 5: Review CloudFront distribution settings.*

### 3. Attach AWS WAF to CloudFront

💡 **Information:** AWS WAF protects the public CloudFront endpoint from common web threats such as SQL Injection, Cross-Site Scripting, and abnormal request patterns. This adds a security layer before traffic reaches the frontend.

Create a WAF Web ACL to protect the public CloudFront endpoint.

1. Open **AWS WAF & Shield**.
2. Choose **Web ACLs > Create web ACL**.
3. Select **CloudFront distributions** as the resource type.
4. Review and create the Web ACL.

⚠️ **Warning:** When protecting CloudFront, the WAF scope must be **CloudFront distributions**. If you choose a regional scope, the Web ACL cannot be attached to CloudFront.

![Create WAF](/images/5-Workshop/5.3-Implementation/Frontend/WAF/createWAF.png?width=90pc&classes=shadow)

*Figure 6: Create a WAF Web ACL.*

Associate the Web ACL with the CloudFront distribution.

1. In the Web ACL, choose **Select resources to protect**.
2. Add the CloudFront distribution.
3. Save the association.

![Connect WAF to CloudFront](/images/5-Workshop/5.3-Implementation/Frontend/WAF/connect-CLF.png?width=90pc&classes=shadow)

*Figure 7: Associate WAF with CloudFront.*

### 4. Configure Cognito authentication

💡 **Information:** Cognito manages authentication for the Helpdesk Portal. It allows users and IT staff to sign in securely, while user groups help separate requester permissions from administrator or staff permissions.

Create a Cognito User Pool to manage user sign-in.

1. Open **Amazon Cognito**.
2. Choose **Create user pool**.
3. Select email as the sign-in method.
4. Configure password policy and user verification.
5. Create the user pool.

![Create Cognito](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/createCog.png?width=90pc&classes=shadow)

*Figure 8: Create a Cognito User Pool.*

Configure the App Client for the frontend.

1. Open the created User Pool.
2. Go to **App integration**.
3. Create or edit the App Client.
4. Use a public client configuration for the web frontend.

![Edit app client](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/edit-appclient.png?width=90pc&classes=shadow)

*Figure 9: Configure the Cognito App Client.*

Configure callback and sign-out URLs.

1. Add the CloudFront URL as the callback URL.
2. Add the CloudFront URL as the sign-out URL.
3. Save changes.

⚠️ **Warning:** The callback URL and sign-out URL must match the frontend URL exactly. A wrong URL can cause Cognito login to fail or redirect users to the wrong page.

![Return URL](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/returnURL.png?width=90pc&classes=shadow)

*Figure 10: Configure callback and sign-out URLs.*

Create user groups and sample users.

1. Create groups such as `User` and `Admin`.
2. Use these groups to separate requester and staff permissions.

![Create group](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/create-group.png?width=90pc&classes=shadow)

*Figure 11: Create user groups for access separation.*

1. Create test users.
2. Assign users to the correct group.
3. Set or reset passwords for testing.

![Create user](/images/5-Workshop/5.3-Implementation/Frontend/Cognito/create-user.png?width=90pc&classes=shadow)

*Figure 12: Create sample users for testing.*

### 5. Create DynamoDB tables

💡 **Information:** DynamoDB stores the main Helpdesk Portal data, including categories, tickets, and ticket comments. It is a good fit for this serverless workload because it scales automatically and does not require database server management.

Create the first DynamoDB table for helpdesk categories.

1. Open **Amazon DynamoDB** and choose **Create table**.
2. Enter the table name `HelpdeskCategories`.
3. Set the partition key according to the backend data model.
4. Choose **On-demand** capacity mode.
5. Create the table.
6. Enable **Point-in-time recovery** if required.

⚠️ **Warning:** Make sure each DynamoDB table name and key matches the backend code. A different name or key will cause the API to fail when reading or writing data.

![Create HelpdeskCategories table](/images/5-Workshop/5.3-Implementation/Backend/DynamoDB/CateDB.png?width=90pc&classes=shadow)

*Figure 13: Create the DynamoDB table for helpdesk categories.*

Create the second DynamoDB table for ticket records.

1. Choose **Create table** again.
2. Enter the table name `HelpdeskTickets`.
3. Set the partition key according to the ticket data model, for example `ticketId`.
4. Choose **On-demand** capacity mode.
5. Create the table.

![Create HelpdeskTickets table](/images/5-Workshop/5.3-Implementation/Backend/DynamoDB/TicketDB.png?width=90pc&classes=shadow)

*Figure 14: Create the DynamoDB table for helpdesk tickets.*

Create the third DynamoDB table for ticket comments.

1. Choose **Create table** again.
2. Enter the table name `HelpdeskComments`.
3. Set the partition key according to the comment data model.
4. Choose **On-demand** capacity mode.
5. Create the table.

![Create HelpdeskComments table](/images/5-Workshop/5.3-Implementation/Backend/DynamoDB/CommentDB.png?width=90pc&classes=shadow)

*Figure 15: Create the DynamoDB table for helpdesk comments.*

### 6. Create S3 attachment bucket

💡 **Information:** The attachment bucket stores files uploaded with tickets, such as screenshots, documents, and error evidence. Keeping attachments in S3 separates large files from ticket metadata in DynamoDB.

Create a private S3 bucket for ticket attachments.

1. Open **Amazon S3** and choose **Create bucket**.
2. Enter the attachment bucket name `Bucket-ticket-h3vt`.
3. Keep **Block all public access** enabled.
4. Enable **Bucket versioning**.
5. Create the bucket.

⚠️ **Warning:** This bucket should stay private because attachments may contain sensitive information. Do not allow public read access.

![Create attachment bucket](/images/5-Workshop/5.3-Implementation/Backend/S3-attachment/createS3-attachment.png?width=90pc&classes=shadow)

*Figure 16: Create the S3 bucket for ticket attachments.*

### 7. Create Lambda

💡 **Information:** Lambda is the main backend component. This function receives requests from API Gateway, validates input, creates or updates tickets, writes data to DynamoDB, processes attachment information, and sends notification messages to SQS.

Create the Lambda function that handles ticket business logic.

1. Open **AWS Lambda** and choose **Create function**.
2. Select **Author from scratch**.
3. Enter the function name, for example `SubmitTicketFunction`.
4. Select the runtime used by the backend.
![Create submit function](/images/5-Workshop/5.3-Implementation/Backend/Lambda-SubmitFunction/createFunction.png?width=90pc&classes=shadow)

*Figure 17: Create Lambda.*

Upload the backend source code.

1. Open the Lambda function.
2. Upload the backend package or source code.
3. Save or deploy the function code.

![Upload backend](/images/5-Workshop/5.3-Implementation/Backend/Lambda-SubmitFunction/uploadbackend.png?width=90pc&classes=shadow)

*Figure 18: Upload backend code to Lambda.*

Configure environment variables for the backend.

1. Add variables for DynamoDB table name and S3 attachment bucket.
2. Save the configuration.
3. Add the SQS queue URL later after the queue is created in step 9.

![Set environment](/images/5-Workshop/5.3-Implementation/Backend/Lambda-SubmitFunction/setting%20environment.png?width=90pc&classes=shadow)

*Figure 19: Configure Lambda environment variables.*

### 8. Create API Gateway

💡 **Information:** API Gateway provides the HTTPS API layer for the frontend. It receives browser requests, verifies authorization through Cognito JWT, and forwards valid requests to the Ticket Handler Lambda.

Create an HTTP API and connect it to the Ticket Handler Lambda.

1. Open **Amazon API Gateway**.
2. Choose **Create API**.
3. Select **HTTP API**.
4. Add Lambda integration and select `SubmitTicketFunction`.

![Create HTTP API](/images/5-Workshop/5.3-Implementation/Backend/API/create-HTTP-API.png?width=90pc&classes=shadow)

*Figure 20: Create the HTTP API.*

Add routes for ticket operations.

1. Create routes such as `ANY /{proxy+}`.
2. Configure Cognito JWT authorizer.
3. Attach the authorizer to protected routes.
4. Deploy the API stage.

⚠️ **Warning:** Do not leave ticket routes unauthenticated. Attach the Cognito JWT authorizer to routes that create, read, or update ticket data.

![Add API route](/images/5-Workshop/5.3-Implementation/Backend/API/add-route-API.png?width=90pc&classes=shadow)

*Figure 21: Add API routes for ticket operations.*

Confirm the Lambda trigger from API Gateway.

1. Open the Lambda function.
2. Add API Gateway so it appears in the trigger section.
3. Save the API endpoint for frontend configuration.

![Add Lambda trigger](/images/5-Workshop/5.3-Implementation/Backend/API/add-triggerAPI-forSubFunction.png?width=90pc&classes=shadow)

*Figure 22: Connect API Gateway to Lambda.*

### 9. Create SQS FIFO queue and DLQ

💡 **Information:** SQS decouples ticket processing from email notification. The Ticket Handler can finish quickly after placing a message into the queue, while the Notification Worker processes email sending asynchronously.

Create the Dead Letter Queue first.

1. Open **Amazon SQS**.
2. Choose **Create queue**.
3. Select **FIFO**.
4. Enter a DLQ name, for example `helpdesk-notification-dlq.fifo`.
5. Create the queue.

⚠️ **Warning:** FIFO queue names must end with `.fifo`. Create the DLQ before configuring it on the main queue.

![Create DLQ](/images/5-Workshop/5.3-Implementation/Backend/SQS/DLQ/createDLQ.png?width=90pc&classes=shadow)

*Figure 23: Create the dead-letter queue.*

Create the main FIFO notification queue.

1. Choose **Create queue**.
2. Select **FIFO**.
3. Enter the main queue name, for example `helpdesk-notification-queue.fifo`.
4. Enable **Content-based deduplication** if suitable.

![Create SQS queue](/images/5-Workshop/5.3-Implementation/Backend/SQS/Queue/createSQS-queue.png?width=90pc&classes=shadow)

*Figure 24: Create the main FIFO notification queue.*

Attach the DLQ to the main queue.

1. In the main queue settings, choose **Dead-letter queue**.
2. Select the DLQ.
3. Set the maximum receive count.
4. Copy the main queue URL.
5. Return to **SubmitTicketFunction** and add/update the SQS queue URL environment variable.

⚠️ **Warning:** If the SQS queue URL is not updated in the Ticket Handler Lambda environment variables, ticket creation may succeed but no notification message will be queued.

![Add DLQ to queue](/images/5-Workshop/5.3-Implementation/Backend/SQS/Queue/add-DLQ.png?width=90pc&classes=shadow)

*Figure 25: Attach the DLQ to the main queue.*

### 10. Create Notification Worker Lambda

💡 **Information:** The Notification Worker Lambda reads messages from SQS and sends email notifications through Amazon SES. This keeps email delivery separate from the main ticket API and makes retry handling easier.

Create the Lambda function that sends email notifications.

1. Open **AWS Lambda** and create a new function.
2. Enter the function name, for example `NotificationWorkerFunction`.
3. Select the runtime used by the notification worker code.
4. Select the prepared Lambda execution role.

![Create notification function](/images/5-Workshop/5.3-Implementation/Notification/NotificationFunction/createFunction.png?width=90pc&classes=shadow)

*Figure 26: Create the Notification Worker Lambda.*

Upload the notification worker code.

1. Open the **Code** tab of the function.
2. Choose **Upload from** and upload the worker source package.
3. Save or deploy the updated code.

![Upload notification worker code](/images/5-Workshop/5.3-Implementation/Notification/NotificationFunction/upload-file.png?width=90pc&classes=shadow)

*Figure 27: Upload the notification worker code to Lambda.*

Configure notification environment variables.

1. Add the sender email variable and the variable linked with Cognito.
2. Save the configuration.

⚠️ **Warning:** If SES is still in sandbox mode, both sender and recipient email addresses must be verified before email delivery can succeed.

![Add notification environment](/images/5-Workshop/5.3-Implementation/Notification/NotificationFunction/add-environment.png?width=90pc&classes=shadow)

*Figure 28: Configure notification environment variables.*

Add the SQS trigger.

1. Choose **Add trigger**.
2. Select **SQS**.
3. Choose the main FIFO notification queue.
4. Save the trigger.

![Add SQS trigger](/images/5-Workshop/5.3-Implementation/Notification/NotificationFunction/add-trigger-SQS.png?width=90pc&classes=shadow)

*Figure 29: Add SQS as the Lambda trigger.*

### 11. Configure CloudWatch and SNS monitoring

💡 **Information:** CloudWatch collects logs and metrics from Lambda and SQS, while SNS sends alert notifications to the team. This helps detect backend errors, queue failures, and notification issues quickly.

Monitor Lambda functions with CloudWatch.

1. Open **Amazon CloudWatch**.
2. Check Lambda log groups and metrics.
3. Create alarms for Lambda errors.

![CloudWatch Lambda](/images/5-Workshop/5.3-Implementation/Monitoring/CloudWatch/CloudWatch-Lambda.png?width=90pc&classes=shadow)

*Figure 30: Monitor Lambda metrics in CloudWatch.*

Monitor SQS and configure SNS notification.

1. Check the main queue and DLQ metrics.
2. Create an alarm for messages in the DLQ.

⚠️ **Warning:** A visible message in the DLQ means the notification flow failed after retries. Always check DLQ messages during validation.

![CloudWatch SQS](/images/5-Workshop/5.3-Implementation/Monitoring/CloudWatch/CloudWatch-SQS.png?width=90pc&classes=shadow)

*Figure 31: Monitor SQS metrics in CloudWatch.*

Monitor Lambda notification with CloudWatch.

1. Check the Lambda notification log groups and metrics.
2. Create an alarm for Lambda notification.

![Alarm notification](/images/5-Workshop/5.3-Implementation/Monitoring/CloudWatch/CloudWatch-notification.png?width=90pc&classes=shadow)

*Figure 32: Configure alarm notification for CloudWatch.*

Configure the SNS subscription used to receive alarm emails.

1. Open **Amazon SNS** and choose the alert topic.
2. Select the SNS topic as the alarm notification target.
3. Check that the subscription protocol is **Email**.
4. Confirm the email subscription.
5. Confirm that the subscription status is **Confirmed**.

![SNS settings](/images/5-Workshop/5.3-Implementation/Monitoring/SNS/settingSNS.png?width=90pc&classes=shadow)

*Figure 33: Configure SNS subscription for alerts.*

### 12. Create CI/CD pipeline

💡 **Information:** The CI/CD pipeline automates source upload, build, and deployment. This reduces manual deployment mistakes and helps frontend and backend updates follow the same release process.

Create the source repository.

1. Open **AWS CodeCommit**.
2. Create a repository.
3. Upload or push the project source code.

![CodeCommit upload](/images/5-Workshop/5.3-Implementation/CI-CD/CodeCommit/create-upload.png?width=90pc&classes=shadow)

*Figure 34: Create repository and upload source code.*

Create the build project.

1. Open **AWS CodeBuild**.
2. Create 2 build projects for frontend and backend.
3. Select the CodeCommit repository as source.

⚠️ **Warning:** Ensure the CodeBuild service role has permission to update S3 and Lambda. Otherwise, the build may succeed partially but deployment will fail.

![Create CodeBuild](/images/5-Workshop/5.3-Implementation/CI-CD/CodeBuild/createCodeBuild.png?width=90pc&classes=shadow)

*Figure 35: Create the CodeBuild project.*

Create and run the pipeline.

1. Open **AWS CodePipeline**.
2. Create a pipeline with CodeCommit as Source and CodeBuild as Build.
3. Run the pipeline with `Release change` and confirm the build succeeds.

![CodePipeline test](/images/5-Workshop/5.3-Implementation/CI-CD/Codepipeline/createCodepipeline-test.png?width=90pc&classes=shadow)

*Figure 36: Configure and run the CI/CD pipeline.*

### 13. Configure KMS and AWS Backup

💡 **Information:** KMS protects stored data with encryption keys, while AWS Backup prepares recovery for important resources. These controls improve data security and reduce the risk of permanent data loss.

Create a KMS key for encryption.

1. Open **AWS KMS**.
2. Choose **Customer managed keys**.
3. Create a key for the project.

![Create KMS](/images/5-Workshop/5.3-Implementation/secure/KMS/createKMS.png?width=90pc&classes=shadow)

*Figure 37: Create a KMS key for encryption.*

Apply KMS encryption to project resources.

1. Apply the key to DynamoDB if required.
2. Apply the key to the S3 attachment bucket.

⚠️ **Warning:** Do not schedule deletion for a KMS key that is still used by S3, DynamoDB, or backups. Data encrypted with that key may become inaccessible.

![Set KMS](/images/5-Workshop/5.3-Implementation/secure/KMS/setKMS.png?width=90pc&classes=shadow)

*Figure 38: Apply KMS encryption to project resources.*

Create AWS Backup resources.

1. Open **AWS Backup**.
2. Create a backup vault for the project.

![Create backup vault](/images/5-Workshop/5.3-Implementation/secure/Backup/createBackup-vault.png?width=90pc&classes=shadow)

*Figure 39: Create the AWS Backup vault.*

1. Create a backup plan.
2. Configure schedule and retention.

![Create backup plan](/images/5-Workshop/5.3-Implementation/secure/Backup/createBackup-plan.png?width=90pc&classes=shadow)

*Figure 40: Create the AWS Backup plan.*

1. Assign the backup vault to the backup plan.
2. Save the backup selection.

![Connect vault plan](/images/5-Workshop/5.3-Implementation/secure/Backup/connect-vault-plan.png?width=90pc&classes=shadow)

*Figure 41: Link the Backup vault to the Backup plan.*
