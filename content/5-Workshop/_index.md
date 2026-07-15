---
title: "Workshop"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# AWS Helpdesk Portal Workshop

#### Overview

In this workshop, you will manually build **AWS Helpdesk Portal**, a serverless IT support ticket management system on AWS. Users can sign in, create tickets, upload attachments, track ticket status, and receive email notifications. IT staff can review, assign, update, and close tickets through the same portal.

The workshop follows the architecture presented by the team:

+ **Frontend:** Amazon S3, Amazon CloudFront, AWS WAF
+ **Authentication:** Amazon Cognito and JWT authorization through Amazon API Gateway
+ **Backend:** Amazon API Gateway, AWS Lambda, Amazon DynamoDB, Amazon S3
+ **Security and resilience:** AWS KMS, AWS Backup
+ **Notification:** Amazon SQS FIFO, SQS DLQ, AWS Lambda Notification Worker and Amazon SES
+ **Monitoring:** Amazon CloudWatch and Amazon SNS
+ **CI/CD:** AWS CodeCommit, AWS CodeBuild, and AWS CodePipeline

#### Content

1. [Workshop overview](5.1-workshop-overview/)
2. [Prerequisites](5.2-prerequisites/)
3. [Implementation steps](5.3-implementation-steps/)
4. [Validation](5.4-validation/)
5. [Clean up](5.5-cleanup/)
6. [Reflection](5.6-reflection/)
