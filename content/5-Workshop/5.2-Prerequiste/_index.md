---
title: "Prerequisites"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### AWS Environment & Local CLI Setup

Before starting this hands-on workshop, ensure you have configured all of the following prerequisites:

### 1. AWS Account & Model Access
*   An active AWS account (Sandbox or Free Tier).
*   **Enable Model Access in Amazon Bedrock**:
    1. Log in to the AWS Management Console and switch to a region supporting Bedrock (e.g., `us-east-1` or `us-west-2`).
    2. Search for the **Amazon Bedrock** service.
    3. In the left navigation pane, select **Model access**.
    4. Click **Manage model access**, select the models you intend to use (e.g., Anthropic's **Claude 3 Haiku** or **Claude 3.5 Sonnet**, and **Amazon Titan**), then click **Request model access**. Wait a few minutes until the status displays *Access granted*.

### 2. AWS CLI & Credentials Setup
*   **Install AWS CLI**: Ensure AWS Command Line Interface is installed on your computer.
*   **Configure Local Profile**: Open your terminal, run `aws configure`, and enter:
    *   `AWS Access Key ID`
    *   `AWS Secret Access Key`
    *   `Default region name` (e.g., `us-east-1`)
    *   `Default output format` (`json`)

### 3. Local Development Tools
*   **Node.js**: Verify that Node.js (version `18.x` or higher) is installed locally (check via `node -v`).
*   **IDE**: Visual Studio Code or your preferred text editor.
*   A personal **GitHub** account (to set up CI/CD pipeline automation for frontend hosting via AWS Amplify).