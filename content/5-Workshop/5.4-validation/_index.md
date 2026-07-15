---
title: "Validation"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Objective
Verify the end-to-end flow of AWS Helpdesk Portal.

### Test scenarios
1. Sign in with Cognito.
2. Submit a new ticket from the portal.
3. Check that the ticket is saved in DynamoDB.
4. Check that notification messages are sent to SQS.
5. Check that the Notification Worker sends email through SES.
6. Check CloudWatch logs and metrics.
7. Confirm that failed messages are moved to DLQ.


### Expected result
The portal can create tickets, store data, send notifications, monitor activity, and handle failed messages safely through DLQ.

### Video demo
https://drive.google.com/file/d/16sIRaHBujSmE8vAJL9YL83sB_UAVMmpr/view?usp=drive_link