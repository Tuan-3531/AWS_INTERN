---
title: "CloudWatch Setup for AWS Lambda"
date: 2026-07-09
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---


In this section, we will review the monitoring architecture and set up a Dashboard in Amazon CloudWatch to monitor the AWS Lambda function.

## Monitoring Architecture

Below is the architecture diagram illustrating the monitoring data flow:

![Lambda to CloudWatch Architecture](/images/5-Workshop/5.6-Lamda-to-CloudWatch/lamdatocloudwatch.jpg)

- **AWS Lambda**: Automatically pushes Logs & Metrics to CloudWatch.
- **Amazon CloudWatch**: Acts as the monitoring center, storing logs and can trigger alarms to Amazon SNS when issues arise.
- **AWS IAM**: Ensures that services communicate securely by granting least privilege execution permissions.
- **Amazon SNS**: Receives error alarms triggered by CloudWatch.

## Step 1: Create a Monitoring Dashboard

1. Navigate to the **CloudWatch** console, select **Dashboards** from the left menu, and create a new one named `AI-Riddle-System-Monitor`.
2. On the empty dashboard, click the **Add a first widget** button to add your first chart.

![CloudWatch Dashboard](/images/5-Workshop/5.6-Lamda-to-CloudWatch/9.png)

## Step 2: Configure the Graph Widget

1. In the **Add widget** dialog:
   - **Data sources types**: Select **Cloudwatch**.
   - **Data type**: Select **Metrics**.
   - **Widget type**: Choose the **Line** graph type.
2. Click the **Next** button to proceed.

![CloudWatch Widget Configuration](/images/5-Workshop/5.6-Lamda-to-CloudWatch/10.png)

## Step 3: Add Lambda Metrics

1. In the metric graph configuration screen, browse or search for the metrics related to the `Create-Riddle` Lambda function.
2. Check the boxes for the following essential metrics to display on the graph:
   - **Errors**
   - **Duration**
   - **Invocations**
3. Finally, click the **Create widget** button. Your new graph will now be displayed on your Dashboard.

![Add Metrics to Widget](/images/5-Workshop/5.6-Lamda-to-CloudWatch/11.png)
