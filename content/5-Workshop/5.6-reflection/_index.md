---
title: "Reflection"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Challenges and Difficulties
- IAM role configuration required the correct permissions for each service.
- API Gateway and Cognito needed the JWT authorizer to be aligned correctly.
- SES sandbox required both the sender and recipient emails to be verified.
- SQS FIFO and DLQ needed to be configured with the correct redrive policy.

### Solutions
- Check CloudWatch logs when Lambda or API errors occur.
- Review the Lambda environment variables again.
- Verify SES identity before testing email sending.
- Monitor SQS and DLQ to check the notification flow.

### Future Development
In the future, this Helpdesk Portal architecture could be expanded with features like:
- **AI/Machine Learning Integration (Amazon Bedrock / Amazon Comprehend)**: to automatically analyze and tag ticket priority based on content.
- **Live Chat**: using Amazon API Gateway WebSockets to support real-time communication between users and IT staff.
- **Data Analytics (Amazon Athena / QuickSight)**: to generate statistical reports on the number of processed tickets and average ticket closing time per staff member.
