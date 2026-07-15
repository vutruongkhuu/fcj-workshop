---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# How Sonar built a unified API on AWS

When reading the article “How Sonar built a unified API on AWS” on the AWS Architecture Blog, the most notable point for me was the way Sonar solved a very practical architecture problem: how to move from a monolithic architecture to multiple independent domains while still exposing services externally through a single unified, secure, and easy-to-operate API.

## Architecture problem

If each domain had its own public API, the system could become harder to manage and would increase the attack surface. For a SaaS product used by many organizations such as SonarCloud, API exposure needs to satisfy three main requirements:

- External users should have one unified endpoint to access.
- Internal teams should still be able to deploy their own domains independently.
- The architecture must be secure, scalable, and reduce operational overhead for the platform team.

Sonar’s solution was to combine Elastic Load Balancing, AWS PrivateLink, and Amazon API Gateway to create a unified API endpoint.

## Overview of the solution

- In this architecture, users access the API through a common domain, for example: api.example.com.
- Behind this domain is an Application Load Balancer (ALB). The ALB is configured with an HTTPS listener and forwards requests to the Elastic Network Interfaces (ENIs) of the VPC endpoint for API Gateway.
- The API Gateway VPC endpoint is deployed across three Availability Zones. Each private subnet has a corresponding ENI. The ALB target group contains the IP addresses of these ENIs.
- On the API Gateway side, Sonar creates a custom domain named api.example.com. Inside this custom domain, they configure API mappings for each domain. For example, api.example.com/domain1 is routed to the private API corresponding to domain1 in API Gateway.
- This design allows the outside world to see only one API endpoint, while internally each domain can still have its own private API Gateway.

## How the request flow works

The request flow in this architecture can be understood through the following steps:

1. First, the user sends a request to api.example.com/domain1. Through Amazon Route 53, this domain is resolved and the request is routed to the Application Load Balancer.
2. Next, the ALB receives the HTTPS request, terminates the connection, and decrypts the request. Then it sends the request to one of the ENIs of the VPC endpoint for API Gateway. At this point, the domain name of the request remains api.example.com and the request path is /domain1.
3. API Gateway uses the custom domain name and API mapping to determine which private API the request should go to. With the path /domain1, the request is routed to the private API of domain1.

From the user’s perspective, they call one shared endpoint. But from an internal architecture perspective, the request is routed to the correct domain.

## Two important API Gateway features

1. Private REST APIs in Amazon API Gateway can only be accessed from inside a VPC through an interface VPC endpoint. This interface endpoint creates ENIs in the VPC.
2. API Gateway custom domains. By default, an API in API Gateway has a URL like https://api-id.execute-api.region.amazonaws.com/stage. With a custom domain, a more understandable URL can be created, such as https://api.example.com/domain1.

This feature is not supported for private REST APIs by default. Therefore, this solution uses a documented workaround from https://github.com/aws-samples/.

Sonar does not expose each private API directly to the internet. Instead, it uses ALB, VPC endpoint, and API Gateway mapping to create an external unified API layer while keeping the backend domain APIs private.

## Why this architecture is useful

- The strength of this architecture is that it solves two needs that may seem contradictory at the same time.
- On one hand, customers need a simple, easy-to-use, and consistent public API. They do not need to know how many domains or teams are operating behind it.
- On the other hand, Sonar’s internal teams can still develop, deploy, and operate their own domains independently. Each domain can have its own private API Gateway, which fits well with a Domain-Driven Design organization.

## Evaluation using the AWS Well-Architected Framework

### Security

- A unified API reduces the attack surface compared to creating a separate public API for each domain. Instead of exposing many public endpoints, the system centralizes access through a single entry point. Using AWS WAF on the ALB helps protect the application from common web exploits. In addition, AWS Shield, enabled by default on Amazon CloudFront, provides a network/transport-layer defense against DDoS attacks.

### Operational Excellence

- This architecture allows each team to deploy application and infrastructure changes behind its own private API Gateway. This reduces operational overhead for the platform team, because it does not need to manage every domain in detail. In addition, the solution is based on AWS managed services, so it can scale automatically as SonarCloud usage grows.

### Reliability

- The solution is built on AWS services that provide high availability by default across multiple Availability Zones in a single region. Request throttling can be configured on each private API Gateway to protect the backend resources from overload.

### Performance

- Amazon CloudFront improves API performance, especially for users far from the deployment region. Traffic travels through the AWS network backbone, helping optimize the path to the ALB.

### Cost

- ALB is used as a single entry point. This can add cost compared with exposing multiple public API Gateways directly. However, this is a trade-off to achieve better security and a more consistent customer experience.

### Sustainability

- By using serverless managed services, Sonar can adjust infrastructure to match actual customer demand. This helps avoid overprovisioning and reduces the environmental impact of the solution.

![Illustration](/images/4.jpg)

Original article: https://aws.amazon.com/vi/blogs/architecture/how-sonar-built-a-unified-api-on-aws/