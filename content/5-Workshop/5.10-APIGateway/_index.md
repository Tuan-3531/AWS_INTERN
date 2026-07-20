---
title : "Create REST API on API Gateway"
date : 2024-01-01 
weight : 10 
chapter : false
pre : " <b> 5.10. </b> "
---


#### Connecting Frontend and Serverless Backend
In this final section of the workshop, you will set up a serverless API endpoint on AWS API Gateway to handle HTTP requests from the Amplify frontend and route them to backend AWS Lambda functions.

We apply the following architectural practices:
- **REST API instead of HTTP API**: We select REST API for its rich enterprise features, including stage variables, client certificates, native request mapping, and robust API key management.
- **Lambda Proxy Integration**: We enable proxy integration. This is optimal because it forwards the raw, unmodified request directly to the Lambda function. The function then parses headers, body parameters, and controls the HTTP response code directly in code, reducing API Gateway config overhead.
- **CORS Enablement**: Enabling Cross-Origin Resource Sharing (CORS) is mandatory because web browsers prevent a script hosted on one domain (Amplify/CloudFront) from making requests to another domain (API Gateway) unless the API Gateway explicitly permits it.

---

### Step-by-Step Configuration

#### Part A: API Gateway setup

1. Open the **API Gateway** console and click **Create API**.

   ![create-api](/images/5-Workshop/5.10-APIGateway/Create%20API.png)

2. Scroll down to find **REST API** (make sure not to select Private REST API) and click **Build**.

   ![rest-api](/images/5-Workshop/5.10-APIGateway/Rest%20API.png)

3. Configure the API settings:
   - **API name**: `AI-Riddle-API`
   - **Endpoint Type**: Choose **Regional** or **Edge-optimized**.
   - Click **Create API**.

   ![api-config](/images/5-Workshop/5.10-APIGateway/AI-Riddle-API.png)

4. Once the API is created, you will see the resources dashboard.

   ![api-created](/images/5-Workshop/5.10-APIGateway/thành%20công%20API.png)

5. Click **Create resource** to build paths representing your application logic endpoints.

   ![create-resource](/images/5-Workshop/5.10-APIGateway/Create%20resoure.png)

6. Create the following paths one by one:
   - `/Create-Riddle`
   - `/riddles`
   - `/export`
   - `/generate`

   ![api-paths](/images/5-Workshop/5.10-APIGateway/API%20theo%20cấu%20trúc.png)

7. Select a resource path (e.g. `/Create-Riddle`) and click **Create method**.

   ![create-method](/images/5-Workshop/5.10-APIGateway/create%20method.png)

8. Configure the method:
   - **Method type**: Choose `POST`.
   - **Integration type**: Select **Lambda function**.
   - **Lambda proxy integration**: Enable the checkbox.
   - Choose the AWS region and search for your backend Lambda function.
   - Click **Create method**.

   ![method-integration](/images/5-Workshop/5.10-APIGateway/thiết%20lập%20method.png)
   ![method-region](/images/5-Workshop/5.10-APIGateway/thiết%20lập%20method%202.png)

9. The method will show up under the resource structure.

   ![method-created](/images/5-Workshop/5.10-APIGateway/thành%20công%20method%20API.png)

10. Click on the path resource and select **Enable CORS** from the Actions.

     ![enable-cors](/images/5-Workshop/5.10-APIGateway/enable%20CORS.png)

11. Check the options for the methods you created (e.g., `POST` and `OPTIONS`), then click **Save**.

     ![cors-settings](/images/5-Workshop/5.10-APIGateway/opption%20CORS.png)

12. Click **Deploy API** to publish your endpoints.

     ![deploy-api](/images/5-Workshop/5.10-APIGateway/Deploy%20API.png)

13. Select **New stage** (or use existing), name the stage (e.g. `dev`), and click **Deploy**.

     ![stage-deploy](/images/5-Workshop/5.10-APIGateway/new%20state%20dev.png)

14. Copy the generated **Invoke URL** (e.g., `https://44vwnl4k95.execute-api.ap-southeast-1.amazonaws.com/dev`).

---

#### Part B: Linking API Gateway with AWS Amplify

1. Go back to the **AWS Amplify** console, select your application, and click **Environment variables** in the hosting sidebar.

   ![amplify-vars](/images/5-Workshop/5.10-APIGateway/Environment%20Variables.png)

2. Click **Manage variables** and click **Add variable**.

   ![manage-vars](/images/5-Workshop/5.10-APIGateway/appsetting.png)

3. Add the variable mapping:
   - **Key**: The variable name expected by your frontend code (e.g., `VITE_API_URL` or `REACT_APP_API_URL`).
   - **Value**: Paste the API Gateway stage Invoke URL.
   - Click **Save**.

   ![save-var](/images/5-Workshop/5.10-APIGateway/value%20theo%20state.png)

4. Trigger a new deployment of your Amplify frontend. 

Your frontend application is now fully integrated with your serverless backend!
