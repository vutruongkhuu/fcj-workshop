---
title: "Prerequisites"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Objective
Prepare the required IAM roles, email verification, and alert channel before deployment.

### 1. Check IAM roles
Open **IAM > Roles** and confirm that the roles for Lambda, CodeBuild, CodePipeline, and AWS Backup are available. These roles allow each AWS service to perform its own task securely.

![IAM roles](/images/5-Workshop/5.2-Prerequisites/IAM/roleIAM.png?width=90pc&classes=shadow)

*Figure 1: IAM roles used by Lambda, CodeBuild, CodePipeline, and AWS Backup.*

### 2. Verify SES email
Open **Amazon SES > Verified identities** and verify the sender/receiver email addresses used for ticket notifications.

![SES identity](/images/5-Workshop/5.2-Prerequisites/SES/SES.png?width=90pc&classes=shadow)

*Figure 2: Verified email identity in Amazon SES.*

### 3. Create SNS topic
Open **Amazon SNS** and create a topic/subscription to receive CloudWatch alarm notifications.

![SNS topic](/images/5-Workshop/5.2-Prerequisites/SNS/SNS.png?width=90pc&classes=shadow)

*Figure 3: SNS topic used for operational alerts.*
