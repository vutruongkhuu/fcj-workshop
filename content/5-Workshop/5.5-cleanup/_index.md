---
title: "Clean up"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Objective
Delete all AWS resources created during the workshop to avoid unexpected costs.

### 1. Delete CI/CD resources
Delete **CodePipeline** first, then delete the **CodeBuild** project and **CodeCommit** repository used by the project.

1. Open **AWS CodePipeline**.
2. Select the Helpdesk Portal pipeline.
3. Choose **Delete pipeline** and confirm.
4. Open **AWS CodeBuild**.
5. Delete the frontend/backend build projects.
6. Open **AWS CodeCommit**.
7. Delete the project repository.

### 2. Delete monitoring resources
Delete CloudWatch alarms, dashboards, and log groups created for the workshop.

1. Open **Amazon CloudWatch**.
2. Go to **Alarms** and delete alarms related to Lambda, SQS DLQ, or API Gateway.
3. Go to **Dashboards** and delete the project dashboard.
4. Go to **Log groups**.
5. Delete log groups for Lambda and CodeBuild if they are no longer needed.

### 3. Delete SNS resources
Delete the SNS topic and subscriptions used for alert notifications.

1. Open **Amazon SNS**.
2. Go to **Topics**.
3. Select the project alert topic and choose **Delete**.
4. Go to **Subscriptions**.
5. Delete remaining subscriptions related to the topic.

### 4. Delete API Gateway
Delete the public API endpoint used by the frontend.

1. Open **Amazon API Gateway**.
2. Choose **APIs**.
3. Select the Helpdesk Portal API.
4. Choose **Delete** and confirm.

### 5. Delete Lambda functions
Remove the SQS trigger before deleting the Notification Worker Lambda.

1. Open **AWS Lambda**.
2. Select the **Notification Worker** function.
3. Go to **Configuration > Triggers**.
4. Delete the SQS trigger.
5. Return to the Lambda function list.
6. Delete the project Lambda functions, such as:
   - `SubmitTicketFunction`
   - `NotificationFunction`

### 6. Delete SQS queues
Delete the main FIFO queue and the Dead Letter Queue.

1. Open **Amazon SQS**.
2. Select the main notification FIFO queue.
3. Choose **Delete** and confirm.
4. Select the DLQ.
5. Choose **Delete** and confirm.

### 7. Delete DynamoDB table
Delete the table used to store ticket data.

1. Open **Amazon DynamoDB**.
2. Choose **Tables**.
3. Select the ticket table.
4. Choose **Delete table**.
5. Confirm deletion.

### 8. Delete S3 buckets
Empty each bucket before deleting it.

1. Open **Amazon S3**.
2. Select the frontend bucket.
3. Choose **Empty** and confirm.
4. Delete the frontend bucket.
5. Select the attachment bucket.
6. Choose **Empty** and confirm.
7. If versioning is enabled, make sure all object versions are removed.
8. Delete the attachment bucket.

### 9. Delete CloudFront distribution
CloudFront distributions must be disabled before deletion.

1. Open **Amazon CloudFront**.
2. Select the frontend distribution.
3. Choose **Disable**.
4. Wait until the status changes to **Disabled**.
5. Select the distribution again.
6. Choose **Delete** and confirm.

### 10. Delete WAF Web ACL
Delete the WAF Web ACL after it is no longer associated with CloudFront.

1. Open **AWS WAF & Shield**.
2. Choose **Web ACLs**.
3. Select the Web ACL used for CloudFront.
4. Confirm that no CloudFront distribution is associated.
5. Choose **Delete** and confirm.

### 11. Delete Cognito User Pool
Delete the Cognito User Pool used for authentication.

1. Open **Amazon Cognito**.
2. Choose **User pools**.
3. Select the Helpdesk Portal User Pool.
4. Choose **Delete user pool**.
5. Enter the required confirmation text.
6. Choose **Delete**.

### 12. Delete SES identities
Delete SES identities only if they were created specifically for this workshop.

1. Open **Amazon SES**.
2. Choose **Verified identities**.
3. Select the email identities used by the project.
4. Choose **Delete** and confirm.

### 13. Delete AWS Backup resources
Delete the backup plan first, then delete the backup vault.

1. Open **AWS Backup**.
2. Choose **Backup plans**.
3. Select the project backup plan and delete it.
4. Go to **Backup vaults**.
5. Select the project vault.
6. Delete recovery points first if the vault is not empty.
7. Delete the backup vault.

### 14. Delete KMS key
KMS keys cannot be deleted immediately. Schedule key deletion instead.

1. Open **AWS KMS**.
2. Choose **Customer managed keys**.
3. Select the KMS key created for the project.
4. Choose **Key actions > Schedule key deletion**.
5. Select a waiting period, for example **7 days**.
6. Confirm deletion schedule.

### 15. Delete IAM roles
Delete only custom roles created for this project.

1. Open **IAM**.
2. Choose **Roles**.
3. Search for project roles, such as:
   - Lambda execution role
   - CodeBuild role
   - CodePipeline role
   - Backup role
4. Open each role.
5. Detach policies if required.
6. Choose **Delete role**.

{{% notice warning %}}
Do not delete AWS service-linked roles unless you are sure they are not used by other resources.
{{% /notice %}}

### 16. Final verification
Search the project name in the following services and confirm that no active resources remain:

- S3
- Lambda
- DynamoDB
- API Gateway
- SQS
- CloudFront
- CloudWatch
- CodePipeline

Finally, check **Billing > Cost Explorer** after a few hours to make sure no unexpected cost remains.

### Expected result
All resources created for AWS Helpdesk Portal are removed successfully. The AWS account no longer contains active resources related to the workshop.
