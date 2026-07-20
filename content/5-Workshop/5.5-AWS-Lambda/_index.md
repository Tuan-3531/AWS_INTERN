---
title: "Deploy AWS Lambda & Configure IAM"
date: 2026-07-09
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---


In this section, we will begin building the core serverless backend logic for the **AI Riddle Generator** project by deploying an **AWS Lambda** function named `Create-Riddle`. This function is responsible for communicating with the AI service **Amazon Bedrock** to generate riddles, storing riddle metadata in **Amazon DynamoDB**, and exporting static riddle files to **Amazon S3**.

To ensure security and scalability, we will configure connection Triggers and grant least-privilege permissions through **AWS IAM Policies**.

---

### Step-by-Step Configuration

#### Step 1: Manage and Initialize the AWS Lambda Function
1. Navigate to the **AWS Management Console** and search for **Lambda** to go to the main dashboard.

   ![AWS Lambda Dashboard](/images/5-Workshop/5.5-AWS-Lambda/1-Lambda-Dashboard.png)

2. Click on the **Functions** tab in the left-hand navigation pane, and click the **Create function** button.
3. Configure the initialization parameters:
   - Select **Author from scratch**.
   - **Function name**: Enter `Create-Riddle`.
   - **Runtime**: Choose **Node.js 18.x** (or a recent LTS version such as 20.x, 22.x).
   - **Architecture**: Keep default `x86_64`.
   - **Permissions**: Leave the default option to automatically create a new Execution role for the Lambda.
4. Click **Create function** at the bottom to save.

   ![Create Lambda function](/images/5-Workshop/5.5-AWS-Lambda/2-Lambda-Create.png)

5. Once the function is created, you will be redirected to the function designer screen.
   *(Note: The following screenshot shows the temporary function name `ABC` used during creation, but the configurations are identical).*

   ![Successful function creation](/images/5-Workshop/5.5-AWS-Lambda/3-Lambda-Success-ABC.png)

---

#### Step 2: Configure Input and Output Triggers
We need to add triggers to connect our Lambda function to other services in our serverless system architecture.

1. **Add Amazon S3 Trigger**:
   - In the Lambda function designer chart, click **Add trigger**.
   - Select the trigger source: **S3**.
   - Select your static file storage bucket: `ai-riddle-storage` (or `riddle-store`).
   - Check the recursive invocation warning acknowledgement box.
   - Click **Add** to confirm.

   ![Configure S3 Trigger](/images/5-Workshop/5.5-AWS-Lambda/4-S3-Trigger.png)

2. **Add Amazon DynamoDB Trigger**:
   - Click **Add trigger** again.
   - Select the trigger source: **DynamoDB**.
   - Select your database table: `AiRiddleGenerator_Core`.
   - Click **Add** to link.

   ![Configure DynamoDB Trigger](/images/5-Workshop/5.5-AWS-Lambda/5-DynamoDB-Trigger.png)

3. **Add Amazon API Gateway Trigger**:
   - Click **Add trigger**.
   - Select the trigger source: **API Gateway**.
   - Choose **Use existing API**.
   - Find and select your project API: `AI-Riddle-API`.
   - Click **Add** to associate API Gateway as the HTTP endpoint gateway leading into your Lambda.

   ![Configure API Gateway Trigger](/images/5-Workshop/5.5-AWS-Lambda/6-APIGateway-Trigger.png)

---

#### Step 3: Implement Backend Business Logic Code
1. Scroll down to the **Code source** section, and open the default `index.mjs` file in the web editor.
2. Replace the default template code with the following Node.js code that invokes Bedrock and interacts with S3 and DynamoDB:

```javascript
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand } from "@aws-sdk/lib-dynamodb";
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";
import { BedrockRuntimeClient, InvokeModelCommand } from "@aws-sdk/client-bedrock-runtime";

const ddbClient = new DynamoDBClient({});
const s3Client = new S3Client({});
const bedrockClient = new BedrockRuntimeClient({ region: "us-east-1" });

const dynamo = DynamoDBDocumentClient.from(ddbClient);
const tableName = "AiRiddleGenerator_Core";
const bucketName = "ai-riddle-storage";

export const handler = async (event) => {
    let body;
    let statusCode = 200;
    const headers = {
        "Content-Type": "application/json",
        "Access-Control-Allow-Origin": "*"
    };

    try {
        let requestJSON = JSON.parse(event.body || "{}");
        const prompt = requestJSON.prompt || "Tạo một câu đố vui tiếng Việt";

        // 1. Invoke Amazon Bedrock to generate riddle text
        const bedrockResponse = await bedrockClient.send(
            new InvokeModelCommand({
                modelId: "anthropic.claude-3-haiku-20240307-v1:0",
                contentType: "application/json",
                accept: "application/json",
                body: JSON.stringify({
                    anthropic_version: "bedrock-2023-05-31",
                    max_tokens: 500,
                    messages: [{ role: "user", content: prompt }]
                })
            })
        );
        const result = JSON.parse(new TextDecoder().decode(bedrockResponse.body));
        const riddleText = result.content[0].text;
        const riddleId = `riddle_${Date.now()}`;

        // 2. Put metadata item in DynamoDB
        await dynamo.send(
            new PutCommand({
                TableName: tableName,
                Item: {
                    PK: `RIDDLE#${riddleId}`,
                    SK: "METADATA",
                    Prompt: prompt,
                    Riddle: riddleText,
                    CreatedAt: new Date().toISOString()
                }
            })
        );

        // 3. Export generated JSON file of the riddle to S3
        await s3Client.send(
            new PutObjectCommand({
                Bucket: bucketName,
                Key: `exports/${riddleId}.json`,
                Body: JSON.stringify({ id: riddleId, prompt, riddle: riddleText }),
                ContentType: "application/json"
            })
        );

        body = { id: riddleId, riddle: riddleText };
    } catch (err) {
        statusCode = 500;
        body = { error: err.message };
    }

    return {
        statusCode,
        body: JSON.stringify(body),
        headers
    };
};
```

3. Click the **Deploy** button on the toolbar to apply the backend updates.

   ![Lambda code source editor](/images/5-Workshop/5.5-AWS-Lambda/7-Lambda-Code.png)

---

#### Step 4: Configure Execution Security Permissions in AWS IAM
By default, the new Lambda function does not have permissions to read/write from DynamoDB, put files in S3, or invoke Amazon Bedrock APIs. We must grant these permissions explicitly.

1. Under the Lambda **Configuration** tab, select **Permissions** from the left-hand menu. Locate the **Execution role** section and click the link in **Role name** (e.g. `Create-Riddle-role-xvuvaltf`). This opens the role configurations in the **AWS IAM Console**.

   ![View execution role details](/images/5-Workshop/5.5-AWS-Lambda/8-Lambda-Permissions.png)

2. **Create and Attach Custom S3 Policy**:
   - In the Role Summary screen, click **Add permissions** -> **Create inline policy**.

     ![Create inline policy](/images/5-Workshop/5.5-AWS-Lambda/9-IAM-Inline-Policy-Create.png)

   - In the Policy Editor, select **S3** as the service.
   - For Actions, check the following object CRUD permissions:
     - `GetObject`
     
       ![Check GetObject](/images/5-Workshop/5.5-AWS-Lambda/10-S3-Policy-GetObject.png)
       
     - `DeleteObject`
     
       ![Check DeleteObject](/images/5-Workshop/5.5-AWS-Lambda/11-S3-Policy-DeleteObject.png)
       
     - `PutObject`
     
       ![Check PutObject](/images/5-Workshop/5.5-AWS-Lambda/12-S3-Policy-PutObject.png)
       
   - Under Resources, select **Specific** and click **Add ARNs** to specify the target S3 bucket (e.g. `arn:aws:s3:::ai-riddle-storage/*`). Proceed to review, name the policy `S3AccessPolicy`, and click **Create policy**.

     ![Add ARNs resource target](/images/5-Workshop/5.5-AWS-Lambda/13-S3-Policy-Resources.png)

3. **Attach AWS Managed Policies**:
   - Back on the Role Details page, click **Add permissions** -> **Attach policies**.

     ![Attach managed policies](/images/5-Workshop/5.5-AWS-Lambda/14-Attach-Managed-Policies.png)

   - Add DynamoDB privileges: Search `DynamoDB` in the search box, and select **AmazonDynamoDBFullAccess** (or `AmazonDynamoDBFullAccess_v2`).

     ![Search and check DynamoDB Full Access](/images/5-Workshop/5.5-AWS-Lambda/15-Attach-DynamoDB-Policy.png)

   - Add Bedrock privileges: Search `bedrock`, and select **AmazonBedrockFullAccess** (or `AmazonBedrockMantleFullAccess` depending on your region).

     ![Search and check Bedrock Access](/images/5-Workshop/5.5-AWS-Lambda/16-Attach-Bedrock-Policy.png)

   - Click **Add permissions** at the bottom. Your Lambda execution role now possesses all three backend operations credentials to securely run the application!
