---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building AI Riddle Generator Application on AWS Serverless Platform

#### Overview

In this hands-on Workshop series, you will be guided step-by-step to build an educational Generative AI riddle creation application (**AI Riddle Generator**). The entire system utilizes the AWS **Serverless** architecture to optimize operational costs and eliminate server management.

You will learn how to design and configure all cloud layers from frontend web UI hosting, security policies, backend microservices, GenAI integrations, database storage, to centralized logging and proactive alerts:

*   **Hosting & CDN**: Distribute the frontend web application via AWS Amplify.
*   **Security (WAF & IAM)**: Filter malicious traffic with AWS WAF and configure least-privilege security roles using AWS IAM.
*   **Authentication & API**: Manage user identity pools via Amazon Cognito and route API endpoints via API Gateway HTTP API.
*   **Backend & AI Layer**: Execute business logic inside AWS Lambda and generate riddle content using Amazon Bedrock API.
*   **Storage & Database**: Persist riddle datasets in Amazon DynamoDB and output printable PDF/Word reports using S3 Bucket Presigned URLs.
*   **Monitoring**: Centralize error metrics via CloudWatch Logs and trigger notification warnings through Amazon SNS.

#### Workshop Chapters

1. [Chapter 5.1: Workshop Overview & System Architecture](5.1-workshop-overview/)
2. [Chapter 5.2: Prerequisites & Initial Setup](5.2-prerequiste/)
3. [Chapter 5.3: Amazon DynamoDB Setup](5.3-amazon-dynamodb/)
4. [Chapter 5.4: Amazon S3 Setup](5.4-amazon-s3/)
5. [Chapter 5.5: Deploy AWS Lambda & Configure IAM](5.5-aws-lambda/)
6. [Chapter 5.6: CloudWatch Setup for AWS Lambda](5.6-lamda-to-cloudwatch/)
7. [Chapter 5.7: Configure Amazon SNS & Alarms](5.7-cloudwatch-to-amazonsns/)
8. [Chapter 5.8: Deploy Frontend to AWS Amplify](5.8-amplify/)
9. [Chapter 5.9: Enable CloudFront, WAF & Route 53](5.9-cloudfront/)
10. [Chapter 5.10: Create REST API on API Gateway](5.10-apigateway/)
11. [Chapter 5.11: Configure Amazon Cognito as Authorizer](5.11-cognito/)
12. [Chapter 5.12: Decommissioning & Cleanup Resources](5.12-cleanup/)