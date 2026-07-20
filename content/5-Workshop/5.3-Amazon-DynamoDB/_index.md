---
title: "Amazon DynamoDB Setup"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---


In this section, we will create an Amazon DynamoDB table and configure a Global Secondary Index (GSI).

## Step 1: Create a DynamoDB Table

1. Navigate to the **Amazon DynamoDB** console, select **Tables** from the left navigation pane, and click **Create table**.
2. In the **Table details** section, configure the following:
   - **Table name**: `AiRiddleGenerator_Core`
   - **Partition key**: `PK` (Data type: **String**)
   - **Sort key**: `SK` (Data type: **String**)

   ![Create DynamoDB table 1](/images/5-Workshop/5.3-Amazon-DynamoDB/1.png)

3. In the **Table settings** section:
   - Select **Customize settings**.
   - **Table class**: Choose **DynamoDB Standard**.
   - **Capacity mode**: Choose **On-demand**.

   ![Create DynamoDB table 2](/images/5-Workshop/5.3-Amazon-DynamoDB/2.png)

4. Click **Create table** to proceed.
5. Once the table is successfully created, its status will show as **Active**.

   ![Table created successfully](/images/5-Workshop/5.3-Amazon-DynamoDB/3.png)

## Step 2: Create a Global Secondary Index (GSI)

To support querying featured riddles, we will create a Global Secondary Index (GSI).

1. Click on the newly created `AiRiddleGenerator_Core` table.
2. Navigate to the **Indexes** tab and click **Create index**.

   ![Create GSI 1](/images/5-Workshop/5.3-Amazon-DynamoDB/4.png)

3. In the **Index details** section, configure the following:
   - **Index name**: `GSI1_FeaturedRiddles`
   - **Partition key**: `GSI1PK` (Data type: **String**)
   - **Sort key**: `GSI1SK` (Data type: **String**)
4. In the **Attribute projections** section:
   - Select **Include**.
   - Click **Add a new attribute** to add the following attributes:
     - `riddle_id`
     - `keyword`
     - `riddle_content`
     - `upvotes`
     - `genre`

   ![Create GSI 2](/images/5-Workshop/5.3-Amazon-DynamoDB/5.png)

5. Click **Create index**. The request to create your index will be submitted, and the GSI status will be **Creating** and change to **Active** shortly.

   ![GSI created successfully](/images/5-Workshop/5.3-Amazon-DynamoDB/6.png)
