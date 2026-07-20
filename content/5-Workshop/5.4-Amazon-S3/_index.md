---
title: "Amazon S3 Setup"
date: 2026-07-09
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---


In this section, we will create an Amazon S3 Bucket to serve as the storage for our project.

## Step 1: Create an Amazon S3 Bucket

1. Navigate to the **Amazon S3** console, select **Buckets** from the left navigation pane, and click the **Create bucket** button.
2. In the **General configuration** section, configure the following:
   - **AWS Region**: Choose `Asia Pacific (Singapore) ap-southeast-1`.
   - **Bucket type**: Choose `General purpose`.
   - **Bucket namespace**: Choose `Global namespace`.
   - **Bucket name**: Enter `ai-riddle-storage` (or a similar name like `riddle-store` depending on your naming convention). Ensure that the bucket name is globally unique across all of Amazon S3.

   ![Create S3 Bucket Configuration](/images/5-Workshop/5.4-Amazon-S3/8.png)

3. Keep the remaining settings at their defaults (such as Object Ownership and Block Public Access) to ensure the security of your data.
4. Scroll to the bottom of the page and click **Create bucket** to proceed.
5. Once created successfully, your new bucket (e.g., `riddle-store` or `ai-riddle-storage`) will appear in your Buckets list.

   ![S3 Buckets List](/images/5-Workshop/5.4-Amazon-S3/7.png)
