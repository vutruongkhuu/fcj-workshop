---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Optimize Global Application Recovery with AWS Global Accelerator

Have you ever experienced a situation where your system crashed in a particular region, or remote users complained about applications spinning due to excessive network latency?

The usual solution is to use traditional DNS (like Route 53) for routing. However, DNS is often affected by the caching mechanisms (TTL Caching) of network providers, causing roaming delays ranging from a few minutes to several hours in case of failure.

In this article, I will share with you an advanced architecture: "AWS Global Accelerator" – a solution that not only optimizes network performance but also helps the system automatically recover (failover) almost instantly based on AWS's global infrastructure.

An Overview of Architecture:

The diagram below illustrates a complete multi-region system that is protected and optimized through AWS Global Accelerator.

The system uses two global static anycast IP addresses as a single endpoint for end users. Behind these is a network of Edge Locations that connect directly to AWS's high-speed backbone network, leading to destination resources (Application Load Balancers - ALB or EC2) located in various AWS Regions.

How does AWS Global Accelerator work?

- Global Static IP Addresses (Anycast IP): Global Accelerator provides you with two fixed static IP addresses. Even if your application expands its coverage area or changes its underlying infrastructure, these IPs remain the same, simplifying client-side firewall configuration.
- Routing via AWS internal network: Instead of letting user traffic "wander" on the unstable public internet, Global Accelerator will capture traffic at the Edge Location closest to the user and route it to AWS's private, secure, and ultra-fast backbone network.

How Do Dynamic Health Checks & Failover Mechanisms Work?

Typically, when a region experiences an issue, DNS systems take time to update. AWS Global Accelerator solves this problem intelligently and proactively:
- Continuous Health Checks: Global Accelerator continuously checks the health status of applications (ALB/EC2) in all Regions.
- Instant Failover: If an application in Region A is detected to be unhealthy, Global Accelerator will immediately isolate that region and redirect 100% of traffic to the healthy Region B. This process takes only seconds, and end users will hardly notice the disruption.
- Flexible Traffic Dials: You can easily configure traffic percentages (e.g., 80% to the primary Region, 20% to the secondary Region) to support Blue/Green deployment strategies or to manage traffic according to needs.

What Benefits Do End Users Receive?
- Smooth, uninterrupted experience: Thanks to the lightning-fast failover mechanism, your application achieves high availability, minimizing downtime.
- Superior access speed: By using the AWS backbone network as a shortcut, latency is significantly reduced, packet loss and lag are eliminated, especially for users in countries far from the origin server.

The system operates in a closed-loop process and is fully automated:

1. End users send requests to the application via "Anycast IP".
2. The nearest "AWS Edge Location" receives the request and checks the routing status table.
3. If all Regions are healthy, the system automatically optimizes the path to direct the user to the "Region with the lowest latency".
4. When a Region experiences a problem, the "Health Check" system detects it and immediately orders that Region to be isolated.
5. Traffic is smoothly and automatically redirected to a safe backup Region.

![Hình ảnh minh họa](/images/1.jpg)
![Hình ảnh minh họa](/images/2.jpg)

Link to the original article: https://aws.amazon.com/vi/blogs/networking-and-content-delivery/maximising-application-resiliency-with-aws-global-accelerator/?content_source=fb&fb_content_id=Q9-wBQFdqBQ2SBnCPxy6A0UN5A51W2ePxGx8rDCk2KPqZwkXKhi67tpncnus-xObJQ&channel_type=fb&fbclid=IwY2xjawSrSFZleHRuA2FlbQIxMABicmlkETFFWXFVbkdQZTh3RGhDSFVMc3J0YwZhcHBfaWQQMjIyMDM5MTc4ODIwMDg5MgABHoi4NP_4y3nSsY2mkdcr8mqDzbJXEJuYTDD6qfaaVhjetmQAjSNRpd1x25I-_aem_d3GeCL--cqIUwpbAwS3pEw