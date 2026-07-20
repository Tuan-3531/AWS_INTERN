---
title: "Decommissioning Resources"
date: 2024-01-01
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

### Decommissioning & Cleaning Up Cloud Resources

Upon completing this hands-on workshop series, clean up all provisioned cloud resources to prevent unexpected billing (primarily driven by active WAF firewall rules, S3 storage, or active hosted Amplify build targets).

Delete your resources in the following sequence:

---

### 1. Delete CloudWatch Alarm & SNS Topic
1. Open the **CloudWatch console** -> **Alarms** -> **All alarms** -> Select the `generateRiddle-HighErrorRate` alarm -> Click **Action** -> Select **Delete**.
2. Open the **SNS console** -> **Topics** -> Select `RiddleAppAlerts` -> Click **Delete**. Ensure associated email subscriptions are also deleted.

---

### 2. Remove AWS Amplify App & DNS Mappings
1. Open the **AWS Amplify console** -> Select your frontend application -> Click **Actions** -> Select **Delete app**.
2. Open the **Route 53 console** (if mapping a custom domain) -> Delete the Alias A Records or CNAME records mapped to the Amplify CDN endpoints.

---

### 3. Delete AWS WAF Web ACL
1. Open the **AWS WAF console** -> **Web ACLs** -> Select your ACL -> Go to **Associated AWS resources** -> Remove the bindings pointing to your CloudFront distribution.
2. Return to the Web ACLs list view, select the ACL, and click **Delete**.

---

### 4. Delete Amazon API Gateway
1. Navigate to the **API Gateway console** -> Select `RiddleHTTPAPI` -> Click **Actions** -> Select **Delete**.

---

### 5. Delete Amazon Cognito User Pool
1. Open the **Cognito console** -> Select `RiddleAppUserPool` -> Click **Delete**.

---

### 6. Remove AWS Lambda Function & IAM Credentials
1. Open the **AWS Lambda console** -> Select the `generateRiddle` function -> Click **Actions** -> Select **Delete**.
2. Open the **IAM console**:
    *   **Roles**: Find `LambdaRiddleRole` and click **Delete**.
    *   **Policies**: Find `LambdaRiddlePolicy` and click **Delete**.

---

### 7. Delete Amazon DynamoDB Table
1. Open the **DynamoDB console** -> **Tables** -> Select the `Riddles` table -> Click **Delete**. Enter the confirmation text to delete the schema and rows permanently.

---

### 8. Remove Amazon S3 Bucket
1. Open the **S3 console** -> Select the `riddle-document-exports-107204` bucket.
2. S3 requires buckets to be completely empty before deletion. Click **Empty** to wipe all files.
3. Once emptied, return to the bucket list view, select the bucket, click **Delete**, and enter the bucket name to confirm permanent removal.

Congratulations on successfully completing the AWS Serverless AI Riddle Generator workshop!
