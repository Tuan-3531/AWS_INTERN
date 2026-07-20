---
title: "Blogs Posted"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


###  [Blog 1 - Why Epic Games Developed Lore and How AWS Helps Optimize Binary Asset Storage](3.1-Blog1/)
This blog introduces Lore, an open-source version control system developed by Epic Games for managing binary assets such as textures, models, animations, and engine binaries. Unlike traditional version control systems, Lore breaks binary files into reusable fragments (chunks) and stores only the modified portions, significantly reducing storage usage, lowering costs, and improving data reuse across multiple projects.
To deploy Lore in the cloud, AWS provides a reference architecture built with Amazon EC2, Amazon ECS, Amazon S3, Amazon DynamoDB, and AWS Cloud Map. This architecture enables scalable deployment, faster data synchronization, and efficient management of large-scale game development projects that handle massive volumes of binary assets.
###  [Blog 2 - Serverless Security Architecture: How to Protect AI Riddle Generator Application on AWS](3.2-Blog2/)
This blog presents a Defense in Depth security architecture for the AI Riddle Generator, a Serverless application built on AWS. The architecture integrates services including AWS WAF, Amazon CloudFront, Amazon Cognito, Amazon API Gateway, AWS IAM, Amazon CloudWatch, and Amazon SNS to provide end-to-end protection across the presentation layer, user authentication, access control, and real-time monitoring.
By applying the principle of least privilege together with centralized authentication and continuous monitoring, the solution helps mitigate security threats, protect high-value AI resources such as Amazon Bedrock, and ensure the application remains secure, scalable, and reliable as user demand grows.
###  [Blog 3 - How one organization cut AWS costs by 39% in 12 weeks](3.3-Blog3/)
This blog explains how a SaaS company reduced its AWS costs by 39% in just 12 weeks by implementing a phased cost optimization strategy. Instead of making all changes at once, the engineering team used AWS Cost Optimization Hub along with services such as AWS Compute Optimizer, AWS Cost and Usage Reports (CUR), and VPC Flow Logs to prioritize low-risk, high-impact improvements across storage, networking, load balancing, and compute resources.
By migrating to EBS gp3, enabling S3 Intelligent-Tiering, configuring Amazon S3 VPC Gateway Endpoints, consolidating Network Load Balancers, rightsizing EC2 instances with AWS Compute Optimizer, and adopting AWS Graviton and Karpenter for Amazon EKS, the organization achieved significant monthly cost savings while establishing a sustainable FinOps governance model to maintain long-term cost efficiency.