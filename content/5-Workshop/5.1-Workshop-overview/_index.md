---
title: "Workshop Overview"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Introduction
The primary goal of this hands-on workshop is to guide you in designing and configuring a complete Serverless architecture integrated with Generative AI on AWS to build the **AI Riddle Generator** application.

Through this exercise, you will understand the mechanics and seamless integrations among cloud serverless services, which optimize cost-efficiency (pay-per-request pricing) and elevate cybersecurity defenses to the highest production standard.

### 2. Solution Architecture Diagram
The diagram below describes the end-to-end data flow starting from user interactions, running through edge protections, routing through API gateways, triggering logic execution, calling AI foundations, and interacting with database tables:

![Comprehensive System Architecture Diagram](/images/5-Workshop/5.1-Workshop-overview/1.png)

### 3. Data Flow Process
1. **Resolution & CDN (Edge)**: Users query the domain resolved by **Route 53**. Static UI requests are served securely via HTTPS by the **CloudFront** CDN from **AWS Amplify**.
2. **Traffic Filtering (WAF)**: An **AWS WAF** firewall intercepts requests at the CDN layer to block DDoS, spam, SQL Injection, and XSS attacks.
3. **Authentication (Cognito)**: Users register or login, obtaining a secure JWT Token from **Cognito User Pools**.
4. **API Gateway**: Requests containing JWT tokens are intercepted by **API Gateway**. The gateway validates token signatures before invoking the backend.
5. **Serverless Compute (Lambda)**: An **AWS Lambda** function receives payload parameters (e.g., keyword topic, target age).
6. **Artificial Intelligence (Bedrock)**: Lambda constructs a structured prompt payload and invokes the **Amazon Bedrock** foundation models.
7. **Storage & DB (DynamoDB & S3)**: Generated riddle content is persisted in **DynamoDB**. File exports (PDF/Word) are generated, stored in a private **S3 Bucket**, and served via short-lived S3 Presigned URLs.
8. **Logging & Monitoring**: Execution logs are forwarded to **CloudWatch**. If errors exceed metrics thresholds, a CloudWatch Alarm notifies administrators via an **SNS** topic to Email or Slack channels.