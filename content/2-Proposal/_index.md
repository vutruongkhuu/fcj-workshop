---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

In this section, you need to summarize the contents of the workshop that you **plan** to conduct.

# PROJECT EXECUTIVE SUMMARY: AWS HELPDESK PORTAL
### **System Architecture:** Serverless Architecture on Amazon Web Services (AWS)

---

### 1. Executive Summary
The **AWS Helpdesk Portal** is a comprehensive IT support request management system (IT Helpdesk) designed and deployed entirely on the Amazon Web Services (AWS) cloud platform using a **Serverless** architectural model. The core objective of the system is to automate the process of receiving, classifying, and resolving internal IT incidents while optimizing the interaction workflow between end-users (Users) and the technical support team (Staff). By fully leveraging managed services from AWS, the system achieves highly flexible auto-scaling, ensures high availability, enforces strict security, and maximizes operational cost efficiency (paying only when processing requests).

---

### 2. Problem Statement
Traditional support request management systems in many organizations today often face significant drawbacks:
* **Fragmented and Manual Processes:** Requests sent through multiple channels (Email, Chat, word-of-mouth) are easily misplaced, lack synchronization, and are difficult to track in terms of status.
* **High Fixed Infrastructure Costs:** Maintaining server systems running 24/7 leads to substantial resource waste during off-peak hours or weekends.
* **Inflexibility under Sudden Traffic Spikes:** Systems are prone to bottlenecks or overloads when wide-area system failures occur, causing a massive influx of tickets at once.
* **Information Security Risks:** Internal incident data or personal staff information that is not properly encrypted is highly vulnerable to data breaches.

---

### 3. Solution Architecture
The system is designed following an Event-Driven Architecture, divided into clear-cut layers:
1. **Frontend & Edge Processing Layer:** Static source code (HTML/ReactJS) is securely stored in **Amazon S3** and distributed through **Amazon CloudFront** edge locations to optimize global response speeds. All traffic passes through **AWS WAF** to prevent common web exploits (SQL Injection, XSS).
2. **Authentication Layer:** Utilizes **Amazon Cognito** to manage user identities and issue JWT Tokens upon successful login.
3. **Compute & API Layer:** **Amazon API Gateway** acts as the entry point for API calls, verifies the validity of JWT Tokens, and routes requests directly to **AWS Lambda (Ticket Handler)** to execute business logic.
4. **Data & Storage Layer:** Ticket metadata (ID, status, description) is stored in the **Amazon DynamoDB** NoSQL database. File attachments and screenshots are uploaded directly to **Amazon S3 (Attachment Storage)**.

---

### 4. Technical Implementation
* **Data Security:** All data-at-rest in DynamoDB and S3 is automatically encrypted using customer master keys managed in **AWS KMS**. **S3 Versioning** is enabled to prevent accidental data overwrites or deletions.
* **Reliability & Redundancy:** **Amazon SQS FIFO** queues guarantee data integrity and strict first-in-first-out sequencing for notification messages, coupled with an **SQS Dead Letter Queue (DLQ)** to isolate corrupted messages for developer inspection. Enabling **Point-in-Time Recovery (PITR)** on DynamoDB alongside **AWS Backup** ensures disaster recovery back to any specific second.
* **Intelligent Monitoring:** The system leverages **Amazon CloudWatch** to monitor real-time performance and centralize logs from Lambda functions. When metrics exceed predefined thresholds, **Amazon SNS** triggers alert notifications directly to DevOps engineers.
* **CI/CD Automation:** The software development lifecycle is fully automated using **AWS CodeCommit** for source control, **AWS CodeBuild** for automated compilation, and **AWS CodePipeline** for seamless, automated deployment to S3 and Lambda, minimizing human error.

---

### 5. Budget Estimation
Due to the pay-as-you-go nature of Serverless architecture, operational costs scale proportionally with actual request volume. Below is the estimated monthly cost for an average workload of **50,000 Tickets/month** (equivalent to roughly 1,000,000 API requests):

| AWS Service | Primary Cost Components | Estimated Monthly Cost |
| :--- | :--- | :---: |
| **AWS Lambda** | ~500,000 executions (Handler + Worker), avg. duration ~200ms | $1.00 |
| **Amazon API Gateway** | ~1,000,000 REST requests | $3.50 |
| **Amazon DynamoDB** | 10 GB storage + Read/Write requests + PITR | $3.50 |
| **Amazon S3** | 50 GB of attachments and Frontend hosting | $1.25 |
| **Amazon CloudFront & WAF** | Data Transfer Out + WAF Web ACL Rules | $12.00 |
| **Other Services** | AWS KMS, AWS Backup, Amazon SQS, CloudWatch, SES (Email) | $5.00 |
| **TOTAL** | **Estimated Monthly Infrastructure Cost** | **~$26.25 / month** |

---

### 6. Risk Assessment
> 💡 **Risk 1: AWS Lambda Cold Starts**
> * **Description:** When the system has no incoming requests for an extended period, the first Lambda invocation takes 1-3 seconds to initialize the environment, causing slight latency for the user.
> * **Mitigation:** Optimize deployment package size, use fast-booting languages (Node.js), and configure *Provisioned Concurrency* for core API paths if necessary.

> 💡 **Risk 2: Uncontrolled Billing Spikes**
> * **Description:** The infinite auto-scaling nature of Serverless could be exploited during DDoS attacks or infinite code loops, leading to unexpected surges in the AWS bill.
> * **Mitigation:** Implement *Rate Limiting* on API Gateway, set *Concurrency Limits* on Lambda functions, and set up an **AWS Budgets** threshold alert (sending email alerts as soon as estimated costs touch $30).

---

### 7. Expected Outcomes
* **40% Reduction in Mean Time to Resolution (MTTR):** Automating distribution workflows via SQS queues and SES alerts helps IT engineers acknowledge and resolve tickets much faster.
* **Over 70% Infrastructure Cost Savings:** Completely eliminates the overhead of idle virtual servers compared to traditional cloud server architectures.
* **High Availability & Fault Tolerance:** The infrastructure naturally inherits Multi-AZ (Availability Zone) deployment from AWS Serverless primitives, ensuring the portal remains resilient and available at a 99.99% SLA.