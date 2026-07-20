---
title: "Blog 2"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Serverless Security Architecture: How to Protect AI Riddle Generator Application on AWS

Hello AWS Study Group VN community.

If you are developing a Serverless application combined with Generative AI — such as our team's automated cognitive riddle generator for kids (AI Riddle Generator) — the challenge is not just how to make the AI run smoothly and write standard Vietnamese prompts. A bigger challenge that developers often ignore in the early stages is: How to secure this architecture comprehensively?

When your application expands to thousands of parents and teachers accessing it, the lack of an access control mechanism, DDoS protection, or loose permissions between services can lead to data leaks, system resource depletion, or sky-rocketing costs of API calls to AI models (like Amazon Bedrock).

To completely solve this problem, applying a multi-layered security model (Defense in Depth) on AWS is mandatory. In this article, we will find out:
- How does the outer security layer (Edge & Presentation) prevent cyber attacks?
- How to manage user identities and protect the backend APIs?
- What is the principle of least privilege between Serverless services?
- How to monitor and send automatic alerts when security incidents occur?

## 1. Edge Security: Stopping Threats at the "Gateway"
As soon as users (Parents, Teachers) interact with the application through the domain name resolved by Amazon Route 53, the system activates the outer defense layers:

*   **AWS Amplify & Amazon CloudFront**: The entire static UI source code (React/Vue) is distributed via CDN. CloudFront automatically supports on-transit data encryption using HTTPS (default SSL/TLS enabled), preventing eavesdropping or data tampering (Man-in-the-Middle).
*   **AWS WAF (Web Application Firewall)**: This shield is configured directly at CloudFront/API Gateway to filter traffic, block bad IP addresses, prevent common attacks like SQL Injection, Cross-Site Scripting (XSS), and set Rate Limiting rules to prevent request spamming.

## 2. User Authentication and Backend API Protection
When a user logs in or requests a new riddle, the Authentication & API layer controls access:
*   **Amazon Cognito**: Handles identity management (User Pool). After a successful login, Cognito issues a valid JWT Token (JSON Web Token).
*   **Amazon API Gateway**: Acts as the "gatekeeper" for the Backend system. API Gateway does not forward requests directly to Lambda. Instead, it cooperates with Cognito to verify the token's validity. If the token is fake or expired, the request is immediately blocked, protecting the Serverless compute layer from unauthorized access.

## 3. Principle of Least Privilege with AWS IAM
The core of securing Serverless architecture lies in isolating the permissions of services within the Serverless Compute and Data Storage layers:

*   **AWS IAM (Identity and Access Management)**: Instead of granting full permissions to the AWS Lambda function, each Lambda function is assigned an IAM Role with minimum necessary permissions (Least Privilege).
*   **Secure Integration Mechanism**:
    *   The `generateRiddle` function (handles riddle creation) only has permission to send prompts to Amazon Bedrock and write data to the specific `Riddles` table in Amazon DynamoDB. It has absolutely no permission to delete data or interfere with other services.
    *   The PDF/Word export function is only allowed to write files to a specific Amazon S3 Bucket (`ai-riddle-storage`) and generate short-lived S3 Presigned URLs (a few minutes) to return to the frontend. Thus, files stored on S3 are kept private, never exposed to the public internet.

## 4. Proactive Security Monitoring with CloudWatch & SNS
A security system is not complete without the ability to monitor and respond quickly:

*   **Amazon CloudWatch**: All activity history and application error codes (e.g., Access Denied errors or abnormal Bedrock API call frequencies) are centralized into CloudWatch Logs & Metrics.
*   **Amazon SNS (Simple Notification Service)**: When CloudWatch detects metrics exceeding safe thresholds (e.g., sudden spikes in 4XX error requests indicating API scanning), the system triggers an Alarm. Through Amazon SNS, an alert email or Slack message is immediately sent to the operations team for timely response.

## 5. Conclusion
Securing a Serverless application integrated with AI like AI Riddle Generator is not just about setting a password for the database. It is a tight integration of a series of encryption, firewall, centralized authentication, and smart authorization solutions:
*   **AWS WAF & CloudFront**: Filter traffic and prevent DDoS from the outside.
*   **Amazon Cognito & API Gateway**: Ensure only valid users can call resources.
*   **AWS IAM**: Limit the blast radius to the lowest level using the principle of least privilege.
*   **CloudWatch & SNS**: Provide comprehensive visibility to detect and respond to incidents in real-time.

Designing a proper security architecture from the beginning helps your application run stably, protects expensive AI resources, and creates a solid foundation for safe product development on the cloud.

---
**References:** [Secure API Access with Amazon Cognito, Amazon API Gateway, and AWS Lambda](https://aws.amazon.com/blogs/compute/secure-api-access-with-amazon-cognito-federated-identities-amazon-cognito-user-pools-and-amazon-api-gateway/)