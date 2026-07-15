---
title: "Overview"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### Objective
Introduce the AWS Helpdesk Portal architecture and the AWS services used in the system.

### Overall Architecture
Users access the portal through CloudFront. The frontend is stored in S3 and protected by WAF. Cognito authenticates users, API Gateway validates JWT tokens, and Lambda processes ticket requests. DynamoDB stores ticket data, S3 stores attachments, SQS FIFO handles notification messages, SES sends emails, and DLQ stores failed messages. CloudWatch and SNS support monitoring, while KMS and AWS Backup improve security and recovery.

![AWS Helpdesk Portal architecture](/images/5-Workshop/5.1-Workshop-overview/architecture.jpg?width=90pc&classes=shadow)

*Figure 1: Overall architecture of AWS Helpdesk Portal.*
