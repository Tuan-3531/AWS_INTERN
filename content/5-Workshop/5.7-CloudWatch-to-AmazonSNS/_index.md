---
title: "Configure Amazon SNS"
date: 2026-07-09
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

Setting up CloudWatch Alarms with Amazon SNS

In this section, we will configure an alarm in Amazon CloudWatch. When our Lambda function encounters errors exceeding the defined threshold, CloudWatch will trigger the alarm and send an email notification using Amazon Simple Notification Service (SNS).

## Step 1: Create a CloudWatch Alarm

1. Navigate to the **CloudWatch** console, select **Alarms** -> **All alarms** from the left navigation pane, and click **Create alarm**.
2. In the **Specify metric and conditions** step:
   - Click **Select metric**.
   - Locate the Lambda metric: Select the `Create-Riddle` function and choose the **Errors** metric.

   ![CloudWatch Alarm 1](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/12.1.png)

   - In the metric configuration, set **Statistic** to `Average` and **Period** to `5 minutes` (or adjust according to your monitoring needs).

   ![CloudWatch Alarm 2](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/12.2.png)

3. In the **Conditions** section:
   - **Threshold type**: Choose `Static`.
   - **Whenever Errors is**: Choose `Greater/Equal (>= threshold)` and enter the value `1`. This means the alarm triggers when there is at least 1 error.

   ![CloudWatch Alarm 3](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/13.png)

4. Click **Next** to proceed.

   ![CloudWatch Alarm 4](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/14.png)

## Step 2: Configure Actions and Amazon SNS

1. In the **Configure actions** step, under the **Notification** section:
   - **Alarm state trigger**: Select `In alarm`.
   - For **Send a notification to the following SNS topic**, select **Create new topic**.
   - **Create a new topic**: Name it `Riddle-API-Error-Alerts`.

   ![Configure Actions 1](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/15.png)

   - **Email endpoints**: Enter the email address you wish to receive alerts at (e.g., `dangquoctuan12t1nh2022@gmail.com`).
   - Click the **Create topic** button. The new SNS Topic will be created and automatically associated with this alarm.

   ![Configure Actions 2](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/16.1.png)

2. Click **Next** to continue.

## Step 3: Add Details and Finalize

1. In the **Add alarm details** step, set the **Alarm name** to `Alert-Riddle-API-Error` and click **Next**.

   ![Add Alarm Details](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/17.png)

2. In the **Preview and create** step, review all your settings and the graph (you will see a red threshold line at 1). Click **Create alarm** to complete the setup.

   ![Preview and Create](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/18.png)

## Step 4: Confirm SNS Subscription

After creating an SNS Topic with an email endpoint, AWS will automatically send a confirmation request to that email address.

1. You can verify the successfully created Topic and Subscriptions in the **Amazon SNS** console.

   ![SNS Topics](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/19.png)

2. Under **Subscriptions**, the status of the new email endpoint will initially be `Pending confirmation`.

   ![SNS Subscriptions 1](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/20.png)

3. Open your email inbox, find the subscription confirmation email from AWS SNS, and click the **Confirm subscription** link.
4. Once confirmed, the subscription status in Amazon SNS will change to `Confirmed`. From this point forward, all CloudWatch error alarms will be routed directly to your email.

   ![SNS Subscriptions 2](/images/5-Workshop/5.7-CloudWatch-to-AmazonSNS/21.png)
